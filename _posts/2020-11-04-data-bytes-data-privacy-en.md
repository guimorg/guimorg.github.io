---
title: "Data Bytes - Data Privacy"
tags: [machine learning, data, privacy, ethics]
toc: true
lang: en
ref: data-bytes-data-privacy
excerpt: A little bit on Data Privacy
header:
  image: "/images/data-bytes/data-privacy/theme.png"

---

(Image taken from [here](https://www.onlineretailtoday.com/security/))

The Data Protection Law in Brazil is already a reality. Effective since [September 18 this year](https://agenciabrasil.ebc.com.br/geral/noticia/2020-09/entenda-o-que-muda-com-a-lei-geral-de-protecao-de-dados), the LGPD defines a set of rules to define limits, conditions for the collection, storage and treatment of personal information. One of the pillars of [LGPD](http://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/L13709.htm) is **data privacy**, according to Art. 1: *"with the objective of protecting the fundamental rights of freedom and privacy"* and also item I of Art. 2: *"The discipline of the protection of personal data is based on: respect for privacy"*.

As a result, the topic of privacy has been widely discussed in various media and also in organizations (mainly due to the legal consequences and image risks); in this short article I will present some concepts related to privacy and also my view on the topic to encourage discussion and the search for more knowledge.

## What is Privacy? (or what Privacy is not)

A good place to start the discussion is to define privacy; this definition alone will separate the best solutions and methods to guarantee data privacy.

### Privacy and Anonymization

It is very important to keep in mind that the concepts of privacy and anonymization are not identical and that anonymized data does not correspond to secure data or privacy. This point is not always very obvious because if we take a *dataset* (a set of data) as independent from others, in a vacuum, anonymization may seem sufficient to guarantee privacy.

However, anonymization fails in privacy when we take other data sources to identify an individual; that is to say that even if a *dataset* is anonymized, information contained in another *dataset* can be used to identify individuals.

A good example of this is the case of the *Netflix Data Challenge*, which provided anonymized data to improve the recommendation system in 2006 (containing only fictional IDs and grades given to users by films); [two University of Texas researchers](https://www.cs.cornell.edu/~shmat/shmat_oak08netflix.pdf), Arvind Narayanan and Vitaly Shmatikov, however, have managed to identify individuals within this anonymized *dataset* using public data [Internet Movie Database](http://www.imdb.com/).

> We demonstrate that an adversary who knows only a little bit about an individual subscriber can easily identify this subscriber’s record in the dataset.
>
> [**Robust De-anonymization of Large Sparse Datasets**](https://www.cs.cornell.edu/~shmat/shmat_oak08netflix.pdf)

The main point of failure of privacy anonymization is that we, individuals, are identifiable using a small number of unique information - that would not necessarily be viewed as sensitive, like the last products you bought on Amazon, or the last films you watched.

This is one of the points raised by Cynthia Dwork, one of those responsible for the concept of Differential Privacy:

> (…) anonymized data (…) either it isn’t really anonymous or so much of it has been removed that it is no longer data
>
> [The Algorithmic Foundations of Differential Privacy](https://www.cis.upenn.edu/~aaroth/Papers/privacybook.pdf)

### Privacy and Encryption

Another point raised is that encryption can be used within the context of privacy. In fact, data encryption is the solution to a problem other than privacy: it solves the problem of *data security*. An important point for LGPD is related to data exposure incidents, that is, the problem of access and protection.

The best way to understand the difference between these two elements is that:

> Encryption protects data, privacy protects identity
>
> [Data Privacy vs. Data Security: What is the Core Difference](https://www.tokenex.com/blog/data-privacy-vs-security)

### But after all, what is Privacy?

The best definition, or the one that best satisfies me, is the differential definition of privacy. This is a mathematical formalization of intuition or definition of privacy and that is why it is so important for the Data context - in which we deal with Machine Learning, Big Data and Algorithms - because it can be translated into machine language.

The details of Differential Privacy are in the book [The Algorithmic Foundations of Differential Privacy](https://www.cis.upenn.edu/~aaroth/Papers/privacybook.pdf), available online (a great reference), but some ideas that lead us to the intuition of this concept:

- The concept of privacy separates the individual from the knowledge learned (obtained through) the individual;
- A consultation should not reveal whether or not an individual is within a *dataset*;
- From an individual's point of view, there is a guarantee that the same consultation would be obtained with or without his information in a *dataset*;
- In other words, everything that can be learned about an individual using the *dataset*, must be able to be learned without access to the *dataset*.

In other words, privacy is seen as the independence of decision or consultation of a specific individual. This *[blog post](https://robertovitillo.com/differential-privacy-for-dummies/)* explains a little better about Differential Privacy at a high level, another great reference on the topic is the first chapter of the book [ "The Ethical Algorithm: The Science of Socially Aware Algorithm Design"](https://www.amazon.com/Ethical-Algorithm-Science-Socially-Design/dp/0190948205) which also has the **consequences** and **disadvantages** of differential privacy.

## Privacy Solutions

Seeking to obtain some form of privacy, there are several solutions that have been adopted over time and look at privacy differently. Because we decided to look for Differential Privacy in the concept of Privacy, these techniques will show and many different flaws in maintaining the identity of individuals within a *dataset*.

- Remove sensitive data: I think this is one of the most general solutions raised for privacy; and perhaps the one that most fails to achieve privacy. The main problem is that it assumes that we individuals are identifiable **only** by sensitive data (which is not true).
- Use only aggregated data: this is another very common practice and consists of using aggregated data in groups of individuals. Here we fail to assume that aggregated data cannot retrieve information from individuals or retrieve the non-aggregated *dataset* (some references [here](https://iapp.org/news/a/aggregated-data-provides-a-false-sense-of-security/) and [here](https://tozny.com/blog/10-unnerving-privacy-fails-thru-data-aggregation/))
- Anonymize data: this point was introduced above with a practical example from the case of Netflix. The main flaw here remains that information contained in other *datasets* can be combined to identify individuals; and yes, this is the responsibility of the company that owns the "anonymous" data.
- Differential Privacy: this is also a technique that uses randomness to achieve a certain level of privacy. One of the shortcomings of this technique is that the simple existence of an individual within a *dataset* **is already informative and identifiable**.

There are other solutions for privacy outside this list (Synthetic Privacy, for example), but they are often out of practicality or usability. [This article](https://pdfs.semanticscholar.org/7c6a/bddbd791dddd281c5764dbe859c55ba2e019.pdf?_ga=2.170032995.299723572.1604518308-1165209624.1601912032) details many of the techniques and is even worth reading for privacy!

## Wrap Up

In this article, I tried to present a little bit about the concept of privacy and also some solutions for privacy. As always, there is no silver bullet for the privacy problem and each of the techniques has flaws and also consequences from the point of view of information (a Machine Learning model, for example, will suffer from accuracy when differential privacy is used).

I hope that it can somehow incite discussion and the search for new references for this topic so important within **Ethical AI** and so relevant to these days.

## References

De Capitani Di Vimercati S, Foresti S, Livraga G, Samarati P. **Data Privacy: Definitions and Techniques**. International Journal of Uncertainty, Fuzziness and Knowledge-Based Systems. 2012;20(06):793-817.

Kearns M, Roth A. **The ethical algorithm**. Oxford: Oxford University Press; 2020.

Tozny. **10 Unnerving Privacy Fails Thru Data Aggregation**. Available at: https://tozny.com/blog/10-unnerving-privacy-fails-thru-data-aggregation/

Dwork C, Roth A. **The Algorithmic Foundations of Differential Privacy**. Foundations and Trends® in Theoretical Computer Science. 2013;9(3-4):211-407.

Narayanan A, Shmatikov V. **Robust De-anonymization of Large Sparse Datasets**. 2008 IEEE Symposium on Security and Privacy (sp 2008). 2008;.

Arbuckle L. **Aggregated data provides a false sense of security**. Available at: https://iapp.org/news/a/aggregated-data-provides-a-false-sense-of-security/

Valente J. **Entenda o que muda com a Lei Geral de Proteção de Dados**. Available at: https://agenciabrasil.ebc.com.br/geral/noticia/2020-09/entenda-o-que-muda-com-a-lei-geral-de-protecao-de-dados

Phillips D. **Data Privacy vs. Data Security: What is the Core Difference?**. Available at: https://www.tokenex.com/blog/data-privacy-vs-security

Vitillo R. **Differential Privacy for Dummies**. Available at: https://robertovitillo.com/differential-privacy-for-dummies/

Harvard University Privacy Tools Project. **Differential Privacy.** Available at: https://privacytools.seas.harvard.edu/differential-privacy