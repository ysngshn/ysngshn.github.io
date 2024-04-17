---
title: "The unbearable effectiveness of ensembling"
categories:
  - Research
tags:
  - publication
  - Bayesian DL
  - deep learning
---

Want to make your neural network models uncertainty-aware? Let me teach you the ultimate technique: just train several copies of your model with different random seeds and average their predictions. Yes, it is this simple.

This approach is called [deep ensemble][cite-de], and it is a deceptively simple and surprisingly effective approach for uncertainty-aware learning. In fact, [Ovadia et al.][cite-can-you] shows that deep ensemble consistently beats all other non-ensemble methods, be it [stochastic variational inference][cite-bbb], [laplace approximation][cite-laplace], [MC dropout][cite-mcdrop], or [post-hoc calibration][cite-calib]. It was a sad day for many BDL researchers.

Still wishing to contribute to the field, I follow the wise proverb "if you can't beat them, join them", and decided to have a closer look at the ensemble approach. This leads to our NeurIPS 2022 paper ["Deep Combinatorial Aggregation"][cite-dca]. And this post is a long-overdue introduction to our work.

Instead of simply repeating the results from the paper, I would like to share some "behind-the-scenes" stories on how this work is conceptualized and gradually developped to this final form. Hopefully my personal experience can be of interest to fellow PhD students and researchers.


## Starting point: rethinking ensemble

During my PhD years, I have realized that there are only two ways to do research: you either generalize a too simple approach, or simplify a too complex one. 

Seeing the simplicity of deep ensemble, the first approach seems to be the way to go.

### The initial idea

This work started with the following initial idea:

>  What if the ensemble is not at teh model level?



### But ... it doen't work!


## What to do with negative results

### In-depth Analysis

Ablation

neuron / layer / multi-layer / modelwise 

### Finding solution

### Changing the perspective

### What if nothing is working?

Open an English dictionary, what is typically the first verb on the word list? Yes, *abandon* :joy:.

Yep, often ideas just don't work. Just note down the results and move on to something new. You can always do a revisit if you come up with another relevant new idea. 

Luckily the idea actually worked this time [TODO] ...


## Conclusion

Until next time :wink:


[//]: # (footnotes:)

[//]: # (Link references:)

[cite-bbb]: https://proceedings.mlr.press/v37/blundell15.html

[cite-calib]: http://proceedings.mlr.press/v70/guo17a.html

[cite-can-you]: https://proceedings.neurips.cc/paper/2019/hash/8558cb408c1d76621371888657d2eb1d-Abstract.html

[cite-dca]: https://proceedings.neurips.cc/paper/2022/hash/d0724f5d6108517c3eab35f77f156967-Abstract.html

[cite-de]: https://proceedings.neurips.cc/paper/2017/hash/9ef2ed4b7fd2c810847ffa5fa85bce38-Abstract.html

[cite-laplace]: https://proceedings.neurips.cc/paper/2021/hash/a7c9585703d275249f30a088cebba0ad-Abstract.html

[cite-mcdrop]: https://proceedings.mlr.press/v48/gal16.html