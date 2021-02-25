# The Embedding Regression FAQ
## (A Work in Progress)

## 1.  What is a la carte embedding?

A la Carte embedding (ALC) was proposed by [Khodak et al. (2018)](https://arxiv.org/abs/1805.05388). In a nutshell, the method takes embeddings which have been pre-trained on large corpora (e.g. word2vec or GloVe embeddings readily available online), combined with a small sample of example uses for a focal word, and then induces a new context-specific embedding for the focal word. This requires only a simple linear transformation of the averaged embeddings for words within the context of the focal word.

## 2.  What is ConText?

_ConText_ is a regression-like hypothesis-testing framework for embeddings. We place ALC in a regression setting that allows for fast solutions to queries of the type “do authors with these covariate values use these terms in a different way than authors with different covariate values? If yes, how do they differ?”
