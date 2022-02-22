# Embedding Regression: Models for Context-Specific Description and Inference

Paper and related materials for [Rodriguez](http://prodriguezsosa.com/), [Spirling](http://www.arthurspirling.org) and [Stewart](https://scholar.princeton.edu/bstewart/home) (2021). The abstract for the paper is as follows

>Social scientists commonly seek to make statements about how a word’s use varies
over circumstances—whether that be time, partisan identity, or some other documentlevel covariate. A promising avenue is the use of domain-specific word embeddings, that
simultaneously allow for statements of uncertainty and statistical inference. We introduce the a la Carte on Text (`conText`) embedding regression model for this purpose.
We extend and validate a simple linear method of refitting pre-trained embeddings to local contexts that requires minimal input data. It outperforms well-known competitors for studying changes in meaning across groups and time. Our approach allows us to speak descriptively of systematic differences across covariates in the context in which words appear. It also allows comments about whether a particular use is statistically significantly different to another. We provide open-source software for fitting the model

You can find the paper [here](https://github.com/prodriguezsosa/EmbeddingRegression/blob/main/Paper/embedregression2.pdf) and a non-technical explainer [here](https://github.com/prodriguezsosa/EmbeddingRegression/blob/master/Explainer/explainer.md).

R software for fitting our models is [here](https://github.com/prodriguezsosa/conText), along with a vignette and links to data sets.

Comments are very welcome: please send us an email, or open an "Issue" here.


