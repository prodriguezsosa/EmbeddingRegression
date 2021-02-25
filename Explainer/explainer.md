# Embedding Regression: A Non-Technical Explainer

Our [paper](https://github.com/prodriguezsosa/EmbeddingRegression/blob/main/Paper/RodriguezSpirlingStewart_EmbedRegression.pdf) is technical in places, but the central ideas are simple and straightforward. Here we give a non-technical account for a social science audience potentially unfamiliar with embeddings.  Below each section, you will find a "too long, didn't read" summary.  


## 1. Meaning as Context (Chips v Crisps)

Suppose a speaker of British (English) and a speaker of American (English) were having a conversation. The British speaker mentions having some "chips". What would the American infer they meant? If the American were completely unfamiliar with the nature of British English, they would take "chips" to mean the deep fried slices of potato that come in colorful plastic bags, and have various flavors like cheese and onion, or barbecue (so, "crisp" in British English). <img align="right" src="chips.jpg" width=100> 
Of course, what the British speaker was *actually* referring to were deep-fried strips of potato typically served hot, and known as French Fries in the US. As the conversation continues, the American speaker notes that the British speaker talks about having "ketchup" with his chips, which he ate with a "knife" and "fork", along with a "cheeseburger", and that he purchased this "dinner" at a well-known "restaurant". These words are rarely used in conjunction with the American understanding of "chips", so as the British speaker proceeds, the American speaker updates that the British speaker must mean something else.

This is a contrived example, but it captures the core insight of the way that a certain branch of linguistics, and subsequently computer science and social science, understands "meaning". In particular, and quoting a famous dictum by [Firth](https://en.wikipedia.org/wiki/John_Rupert_Firth) "You shall know a word by the company it keeps." The central idea, given a more formal name as the ["Distributional Hypothesis"](https://aclweb.org/aclwiki/Distributional_Hypothesis), is that humans give meaning to words by understanding the other words around them in text. In our example then, the American speaker inferred that "chips"---as used by his British colleague---are somehow connected to "ketchup", "knife", "fork", "cheeseburger", "dinner" and "restaurant". And thus are probably not what he understands "chips" to be.

There are two immediate observations here. First, for this to work, some things have to be kept *constant*. That is, for the American to update that "chips" for the British speaker mean "fries", the meaning of "ketchup", "cheeseburger", "dinner" etc have to be similar across the two language varieties. If this is not true then it will be hard, maybe impossible, to learn anything concrete. Second, what a word *means* and how it is *used* are treated essentially synonymously in this example. That is, if someone uses the term "chips" the way---i.e. with a similar set of context words---the British speaker uses it, they mean "chips" in the same way. If they use it in the way the American uses it, they "mean" chips in that way. Put otherwise, we are not trying to infer meaning in a "deep" sense, such as what happens in a speaker's brain when they hear or say a certain term. Rather we are doing a simple but informative comparison of language patterns in practice.

How can we be more formal about this idea of meaning? How can we model word use in a way that automatically gives us this intuitive understanding of context, and similarity or difference? The answer in recent time is "word embeddings."

**tl;dr: the context of a word tells us about its meaning**

## 2. Embeddings as Meanings

If humans understand words by the words that appear around those words, this offers a path to think about modeling meaning statistically. Specifically, we would like to have an automated way to look at a whole corpus of documents, and take as an input the context words (like "ketchup" and "cheeseburger") and learn that a particular target (like "chips") was very likely to occur near by. Or learn that this was not likely. At a high level of abstraction, that is what ["word embedding"](https://en.wikipedia.org/wiki/Word_embedding) techniques do. More specifically, they produce a vector of numbers for every single word in the corpus. This vector might be 100 or 300 numbers long, or even longer. As it turns out, vectors which are close (i.e. contain similar values of the numbers) represent words that appear in similar contexts. And, per our comments above, they mean similar things. So, for the British speaker, the vector for "chips" is close to "ketchup" but quite far from the vector for "bag". Presumably the opposite is true for the American speaker.

Generating these vectors is not always easy, however. When a word is rare, we do not have much information to go on, and modeling this with embedding techniques will not be very informative. For example, suppose our British speaker had mentioned having "chips at dinner", and said nothing more on the matter. Though it is not especially likely they had a bag of chips (in the US sense) at dinner time, it is far from impossible. Consequently, we may find ourselves not much clearer on what was meant by "chips". In reality, in a large corpus---say, millions of restaurant reviews or wikipedia entries or magazine articles---we would see "chips" enough in the British sense to generate a useful embedding. After all, "chips" is not a rare word in either version of English. But given a small corpus, such as a hundred speeches and/or a relatively rare word (like ["abjure"](https://en.wiktionary.org/wiki/abjure)), we might struggle.

What should we do about this problem of generating embeddings? One possibility is to just get more data. But there may be a firm upper limit on legislative speeches, or tweets from a particular cultural community. Alternatively, we may be able to use the information we currently have to infer what the embedding would have been. A key finding here is from a paper by [Arora et al (2016)](https://www.aclweb.org/anthology/Q16-1028/). Those authors find that, under some plausible assumptions, one can obtain an embedding for any word just be taking the average of the embeddings of the words around it. And because the words around rare words won't generally be rare themselves, we can use the embedding vectors we have for those, to infer an embedding vector for the word in question. In our running example, we will likely have an embedding vector (possibly from American English) for every context word around "chips" as used by the British speaker---so an embedding "ketchup" and an embedding for "dinner" and "cheeseburger" and "fork" etc. If we take the average of those vectors, we get a new vector, which the inferred embedding for how the British speaker is using "chips". That embedding vector will be of a similar value to the word "fries" in our American corpus, and thus we will "translate" across the variants of English.

Unfortunately, the averaging approach does not quite work in practice. In particular, one has to reweight the other vectors according to their frequency in the corpus: essentially very common words (like "and" and "the") do not help much, and need those embeddings need be weighted down when one takes the average. This observation is made by [Khodak et al](https://arxiv.org/abs/1805.05388). Those authors solve this problem by showing how to estimate a re-weighting matrix for the entire corpus, quickly and efficiently. Once one has this matrix, simply multiplying it by the embeddings of the context words (the embedding vectors of "dinner" and "ketchup" etc) will yield a high quality "a la carte" embedding for the word of interest. As we will now see, this is very helpful for problems in which one wants to understand how different groups understand words differently.

**tl;dr: we can measure the context of a word using a word embedding. If we do not have an embedding for a particular (rare) word, we can get one by taking the weighted average of the embeddings around it in text.**


## 3. Embeddings in Regression

Usually in social science, if we want to measure how members of different groups differ with respect to some outcome, we run a [(linear) regression](https://en.wikipedia.org/wiki/Linear_regression) of the following form:

<p align="center">
Y = &beta;<sub>0</sub> + &beta;<sub>1</sub> X<sub>1</sub> + error
</p>

Here, X<sub>1</sub> is our group membership variable, and for now let us assume it is binary. The coefficient, &beta;<sub>1</sub> (or our estimate of it), tells us how the outcome (Y) differs, on average, for different groups. Of course, we might put many more variables on the right hand side---and thus have a *multiple* regression---but the basic logic is the same.

To extend our running example, suppose we have a large number of American and British English speakers and speeches. We want to know how those two *groups* differ in terms of their understanding of the word "chips". We made the point above that the embedding of "chips" is one way of numerically recording its meaning. And we also said that, even if "chips" is a rare word, we can still obtain an embedding for it via the a la carte technique mentioned. In fact, in some experiments we did, we found that we can get good embeddings for every *use* of every term by every speaker. Analogously, we can get a reasonable representation of "chips" for every single time a person uses that term, so long as we know the context in which they used.

The problem now is an obvious one: the (linear) multiple regression framework above accepts a single outcome value for each observation. But embeddings are long: as we said, typically vectors with length of 100 or 300. The solution is to move to a [*multivariate* regression framework](https://rss.onlinelibrary.wiley.com/doi/pdf/10.1111/1467-9868.00054), common when one has multiple responses. The key idea is that, now, the dimensions of the beta coefficient also expand: it is the same length as the vectors we are using for the embeddings themselves. Running this regression is now very fast and straightforward, but some care is required in interpreting its results.

**tl;dr: we put things into a *multivariate* regression by making each observation of the data an embedding (as the outcome), with the group membership (as the predictor, or covariate).**

## 4. Our Approach: ConText

In practice, the multivariate regression above requires some effort to interpret. In the usual *multiple* regression set up, the coefficient on the group membership is the average effect on outcome for a one unit change in membership (i.e. moving from whichever group is coded zero, to whichever is coded 1). And then we can talk about the *predicted* value for the base group being the value of the constant, with the value for the other group being the constant plus the coefficient.

This will not work here, for two reasons. First, because as we said, we have a vector for each of the beta hat so it is not a simple matter of adding two (or a few more) numbers. Second, embeddings are not outcomes in an *absolute* sense. Instead, embeddings are interpreted in terms of *relative* closeness: for example, for the British English speaker, "chips" are close to "ketchup" *relative* to "bag" (and this relative difference is presumably reversed for the American speaker). So interpreting our beta_hat must be in these terms. The way we solve this is to think in use the [Euclidean "norms"](https://en.wikipedia.org/wiki/Norm_(mathematics)#Euclidean_norm) of the estimated &beta;s. This simple transformation, by definition, measures the distance from the "origin" (the vector of all zeros in whatever dimension we are working) to our coefficient. And this allows us to make statements about whether this beta_hat differs from zero or not, in a statistical sense. In practice we use a permutation procedure to generate *p*-values for these claims.

This entire approach, we refer to as our ConText (a la [C]arte [on] Text) model. 

**tl;dr: our ConText regression approach to producing coefficient norms allows us to make statements about statistical significance and p-values. In this way one can do hypothesis tests.**

## 5. Some Results: Congressional Meaning

Just as one example of what the ConText model can do, suppose we wanted understand how Congressional Democrats and Republicans differ in their understanding of "immigration". As we noted above, we obviously cannot know in a "deep" sense whether what they mean by the concept varies across parties. But we can look at *use* patterns, and make inference from those. We can do this for any word, and our priors are naturally that for more politically controversial words, the parties differ in way that is less true of function words like "the" or "and".

We find this to be true, and the figure below presents the normed coefficients. 

It it also easy to give the "nearest neighbors" of any term for the two different groups. And we do that in the table below.


