---
title: Exploring Pallas extension from JAX
last_modified_at: 2025-02-27
categories:
  - Programming
tags:
  - coding
  - deep learning
  - Python
  - JAX
  - Pallas
---

Out of curiosity, I took some time to explore the new [Pallas](https://docs.jax.dev/en/latest/pallas/index.html) extension from [JAX](https://github.com/jax-ml/jax) by benchmarking several implementations of a custom RNN architecture called [IndRNN](https://arxiv.org/abs/1803.04831) (Feel free to also check out my [PyTorch-CUDA implementation](https://github.com/ysngshn/torch-indrnn)). It was a lot of fun, and I would like to share my experience in this post.

The benchmark source code can be found here: [https://github.com/ysngshn/jax-pallas-benchmark](https://github.com/ysngshn/jax-pallas-benchmark)

## What is Pallas?

Standard JAX focuses on efficiently computing tensor operations, similar to NumPy and PyTorch. While this covers many deep learning and big data use cases, sometimes we might want to have more fine-grain control over the usage of computational hardware for better efficiency.

One way is to go back to low-level languages such as CUDA so one can express exactly how things should be done on the hardware. However, this is typically more hardware-specific and less general, has a steep learning curve, and is hard to "get it right" for non-experts. Thus it might be interesting to find a "middle ground" for better trade-off between expressiveness and ease of use.

This gives rise to projects like [Triton](https://github.com/triton-lang/triton). And Pallas is another proposal from the JAX team.

What exactly is this "middle ground"?

I would summarize it as an abstraction of computational hardware: we assume that it is composed of many "computational cores" that can compute concurrently in parallel. Each computational core has its own local memory, and there is a shared global memory where inputs and final outputs should be held. We then have a single instruction multiple data (SIMD) regime, where we need to specify what should be passed between the global memory and each local one on the core, as well as how to execute the computations on each core. One can hope to achieve better efficiency by properly using all available computational cores together and controlling the costly data transfer between global and local memories.

From my understanding, in the case of NVIDIA GPUs, such "computational cores" correspond to streaming multiprocessors (SMs).

## Benchmark task: IndRNN

One specific example that highlights the limitations of tensor-based computations is the [independently RNN model](https://arxiv.org/abs/1803.04831), where its recurrent computations can be executed independently for each data feature. In this case, we would wish to skip any unnecessary synchronization at each time step so that each computational core can run without interruption. This is however hard to achieve in a purely tensor-based setup, since one typically needs to iterate over the time steps and can only do parallel tensor computations within each step. We would thus expect significant improvement by using the "middle ground" through Pallas.

To verify this, I have implemented three different versions of IndRNN in JAX. They are available in the [run_benchmark.py](https://github.com/ysngshn/jax-pallas-benchmark/blob/main/run_benchmark.py) script:

- [indrnn_naive](https://github.com/ysngshn/jax-pallas-benchmark/blob/main/run_benchmark.py#L53) is the naive implementation using a Python for loop.
- [indrnn_scan](https://github.com/ysngshn/jax-pallas-benchmark/blob/main/run_benchmark.py#L87) uses the `jax.lax.scan` structured control flow primitive. Note that it will automatically compile the step function to optimize its iterative computations.
- [indrnn_pallas](https://github.com/ysngshn/jax-pallas-benchmark/blob/main/run_benchmark.py#L120) is the Pallas-based synchronization-free version. The computation on each core is implemented by the [_indrnn_elementwise_kernel](./run_benchmark.py#L23) Pallas kernel function.

Running them 10000 times on a random input with 1000 times steps and 1000 features yields the following run times on my machine:


| version | run time (s)       |
|---------|--------------------|
| naive   | 299.2141582071781  |
| scan    | 35.74692644178867  |
| pallas  | 30.580906197428703 |

We see that Pallas-based implementation is indeed significantly faster than iterative tensor compute variants.

What happens when we compile these implementations?

Using `jax.jit` on each of the implemented functions, we observe the following run times using the same setup:

| version | run time (s)        |
|---------|---------------------|
| naive   | 0.20412501879036427 |
| scan    | 5.375245346687734   |
| pallas  | 0.8124916255474091  |

Yep, `jax.jit` is crucial if you want to run things fast in JAX! Additionally, we see that the Pallas version is again faster than the scan version, which further validates our analysis.

Surprisingly, the jit-compiled naive version with Python for loop is the fastest. This is because Python for loop is unrolled during compilation, and the underlying compiler is able to do a thorough optimization, possibly removing the unnecessary synchronizations in the process. The caveat in this case is the long compile time that grows super-linearly with increasing time steps (you will feel the pain especially if you rerun the script with even larger time steps, e.g., `nt = 10000` instead of `1000`).

## Grids and Blockspecs in Pallas


> ![Official illustration from Pallas for matmul operation.](https://docs.jax.dev/en/latest/_images/BlockSpec.png)
>
> (Image source: [official Pallas tutorial](https://docs.jax.dev/en/latest/pallas/quickstart.html))

To schedule the SIMD computation and specify the data transfer between global and local memories, Pallas introduces the notion of grid and "Blockspec": program instances to be executed on computation cores are arranged and indexed as a grid. Inputs and expected outputs are partitioned evenly into blocks, and these blocks are associated with their corresponding jobs via index matching. The partition and index matching for each input/output is specified by a "Blockspec" in Pallas.

In the previous `indrnn_pallas` implementation, we use a default configuration which completely partitions all features of the input, resulting in 1000 data blocks of shape `(1000, 1)`. The sequence dimension is kept together due to the temporal dependency in the RNN computation.

What if we change the block shape, let's say to `(1000, 4)` instead?

Here are the results on my computer before jit-compile:

| block shape | run time (s)       |
|-------------|--------------------|
| (1000, 1)   | 30.580906197428703 |
| (1000, 4)   | 30.100397765636444 |

And after jit-compile, we have:

| block shape | run time (s)       |
|-------------|--------------------|
| (1000, 1)   | 0.8124916255474091 |
| (1000, 4)   | 0.4490365516394377 |

We see that the new configuration achieves better performance for my setup, especially in the compiled case.

This observation, as well as optimal configurations, will most likely depend on the specific hardware being used. I am no hardware expert, and I would appreciate it if you have any guides or heuristics on how to choose the grid/block size ;)

## Conclusion

At the time of this writing, the Pallas functionalities are still experimental, so things might change in the future. I have encountered several restrictions, such as not being able to use operations like `jax.lax.scan` or slicing the arrays in the kernel function. Also, long Python `for` loops in Pallas kernel are ridiculously slow and one should use `jax.lax.fori_loop` instead (try replacing the `fori_loop` in [the indrnn Pallas kernel function](https://github.com/ysngshn/jax-pallas-benchmark/blob/main/run_benchmark.py#L47) and be prepared to wait for an hour before you get the result!). Despite all these issues, I think Pallas shows a lot of promise, and I can't wait to see it getting improved and being used in more cases.

Until next time :wink:

## References

- [Pallas benchmark Github repo](https://github.com/ysngshn/jax-pallas-benchmark)
- [Official JAX Pallas doc](https://docs.jax.dev/en/latest/pallas/index.html)
- [Triton project](https://github.com/triton-lang/triton)
- [IndRNN model](https://arxiv.org/abs/1803.04831) and [its official PyTorch implementation](https://github.com/Sunnydreamrain/IndRNN_pytorch).
