# Introduction to Language Processing - Summary

## Official Microsoft Learn Page

- https://learn.microsoft.com/en-us/training/modules/introduction-language/2-how-it-works?pivots=text

---

# Overview

This page introduces the fundamentals of **Natural Language Processing (NLP)** and explains how text is prepared for AI and machine learning systems.

The main concept presented is **tokenization**, followed by several common preprocessing techniques used in NLP pipelines.

---

# 1. Tokenization

Tokenization is the process of splitting text into smaller pieces called **tokens**.

Example sentence:

```text
"We choose to go to the moon"
```

Becomes:

1. We
2. choose
3. to
4. go
5. to
6. the
7. moon

Each token can then be analyzed statistically.

The page explains that repeated words (such as `"to"`) can help identify frequency patterns and topics within a text corpus.

---

# 2. Text Normalization

Normalization standardizes text before analysis.

Typical normalization steps include:

- converting text to lowercase
- removing punctuation
- simplifying formatting

Example:

```text
Banks -> banks
```

Purpose:
- improves consistency
- reduces duplicate representations of the same word

Tradeoff:
- may remove useful semantic distinctions

---

# 3. Stop Word Removal

Stop words are very common words with limited semantic value.

Examples:

- the
- a
- it
- and

Removing stop words helps NLP models focus on more meaningful content.

---

# 4. N-Grams

N-grams are groups of words analyzed together.

Types:

| Type | Example |
|---|---|
| Unigram | artificial |
| Bigram | artificial intelligence |
| Trigram | natural language processing |

Purpose:
- preserve context
- improve phrase understanding

---

# 5. Stemming

Stemming reduces words to a simplified root form.

Examples:

| Original Word | Stem |
|---|---|
| powering | power |
| powered | power |
| powerful | power |

Characteristics:
- fast
- simple
- may produce non-dictionary roots

---

# 6. Lemmatization

Lemmatization also reduces words to a base form, but uses linguistic rules.

Examples:

| Original Word | Lemma |
|---|---|
| running | run |
| global | globe |

Characteristics:
- more accurate than stemming
- computationally more expensive

---

# 7. Parts of Speech (POS) Tagging

POS tagging identifies the grammatical role of each token.

Examples:

- noun
- verb
- adjective
- adverb

Purpose:
- improves contextual understanding
- helps with sentence structure analysis

---

# Key Takeaways

The page emphasizes that NLP is not only about splitting text into words.

Practical NLP systems usually combine:

- tokenization
- normalization
- stop-word removal
- n-grams
- stemming
- lemmatization
- POS tagging

These preprocessing techniques help AI systems better understand and analyze human language.

---

# Additional Official Microsoft Learn References

## NLP Overview

- https://learn.microsoft.com/en-us/azure/ai-services/language-service/overview

## Azure AI Language Service

- https://learn.microsoft.com/en-us/azure/ai-services/language-service/

## Text Analytics Concepts

- https://learn.microsoft.com/en-us/azure/ai-services/language-service/concepts/model-lifecycle

## Microsoft AI Fundamentals Learning Path

- https://learn.microsoft.com/en-us/training/paths/get-started-with-artificial-intelligence-on-azure/

---
---

# Statistical Techniques in NLP

## Core Concept

Statistical NLP uses mathematical and probabilistic methods to analyze language.

Instead of relying purely on manually written linguistic rules, statistical approaches learn patterns from large text datasets (corpora).

These techniques are foundational to:
- text classification
- sentiment analysis
- translation
- search engines
- autocomplete
- speech recognition
- chatbots
- large language models (LLMs)

---

# Bag of Words (BoW)

A common statistical representation technique.

## Idea

A document is represented by:
- its vocabulary
- frequency of words

Word order is ignored.

Example:

Sentence:
> "The cat sat on the mat"

Possible representation:

| Word | Count |
|---|---|
| the | 2 |
| cat | 1 |
| sat | 1 |
| on | 1 |
| mat | 1 |

## Characteristics

### Advantages
- Simple
- Fast
- Easy to implement

### Limitations
- Loses context
- Ignores grammar
- Ignores word order
- Cannot distinguish semantic meaning

Example:
- "dog bites man"
- "man bites dog"

May appear similar in BoW.

---

# TF-IDF (Term Frequency–Inverse Document Frequency)

Improves Bag of Words by reducing importance of overly common words.

## Purpose

Identify words that are:
- frequent in one document
- uncommon across all documents

This highlights more meaningful terms.

---

## Formula Components

### Term Frequency (TF)

Measures how often a term appears in a document.

High frequency → potentially important.

### Inverse Document Frequency (IDF)

Reduces weight of terms appearing in many documents.

Common words:
- the
- is
- and

receive lower importance.

---

## TF-IDF Interpretation

High TF-IDF score:
- term is important to a specific document

Low TF-IDF score:
- term is common everywhere

---

# Frequency-Based Analysis

Statistical NLP often analyzes:
- token frequency
- phrase frequency
- word co-occurrence

These statistics help detect:
- document topics
- language patterns
- semantic associations

Example:
- "Azure"
- "cloud"
- "AI"

appearing together frequently suggests related concepts.

---

# N-grams

N-grams capture small sequences of words.

## Types

| Type | Example |
|---|---|
| Unigram | "machine" |
| Bigram | "machine learning" |
| Trigram | "natural language processing" |

## Importance

N-grams partially preserve context and word order.

Useful for:
- predictive text
- speech recognition
- machine translation

---

# Statistical Language Models

A statistical language model predicts the probability of a sequence of words.

Example:

Probability estimation:

P(word | previous words)

Used for:
- autocomplete
- next-word prediction
- speech-to-text
- translation systems

Traditional approaches heavily used:
- bigram models
- trigram models

Modern transformer-based LLMs evolved from these ideas.

---

# Traditional Statistical NLP vs Semantic/Neural NLP

## Traditional Statistical NLP

Focuses on:
- frequencies
- probabilities
- token counts
- co-occurrence

Examples:
- Bag of Words
- TF-IDF
- N-grams

### Limitations
Weak understanding of:
- context
- sarcasm
- ambiguity
- semantic meaning

---

## Modern Semantic / Neural NLP

Uses:
- embeddings
- transformers
- neural networks
- attention mechanisms

Captures:
- context
- relationships
- semantic meaning

Examples:
- BERT
- GPT
- Azure AI Language models

---

# AI-900 Exam-Relevant Distinctions

| Concept | Key Idea |
|---|---|
| Tokenization | Split text into tokens |
| Bag of Words | Frequency-based representation |
| TF-IDF | Weighted importance of words |
| N-gram | Sequence of adjacent words |
| Statistical NLP | Probability/frequency driven |
| Semantic NLP | Context and meaning driven |

---

# Common Misconceptions

## "Statistical NLP understands meaning"

Incorrect.

Traditional statistical methods mainly detect:
- patterns
- frequencies
- probabilities

Deep semantic understanding is limited.

---

## "Bag of Words preserves sentence meaning"

Incorrect.

BoW ignores:
- grammar
- syntax
- word order

---

## "TF-IDF removes common words"

Not exactly.

It reduces their importance rather than removing them entirely.

---

# Architecture Perspective

Traditional statistical NLP:
- lighter computationally
- easier to train
- interpretable

Modern neural NLP:
- significantly more accurate
- context-aware
- computationally expensive

Azure AI services primarily use modern transformer-based approaches internally.

---

# Key Takeaways

- Statistical NLP analyzes language using probabilities and frequencies.
- Bag of Words converts text into frequency vectors.
- TF-IDF improves keyword importance scoring.
- N-grams help preserve local context.
- Statistical language models predict word sequences probabilistically.
- Traditional statistical NLP is foundational but limited semantically.
- Modern NLP systems use neural networks and transformers for contextual understanding.

---

# Official References

## Microsoft Learn

- Introduction to NLP concepts:
  https://learn.microsoft.com/en-us/training/modules/introduction-language/

- Statistical text analysis unit:
  https://learn.microsoft.com/en-us/training/modules/introduction-language/3-statistical-techniques?pivots=text

## Microsoft Azure

- What is NLP:
  https://azure.microsoft.com/en-us/resources/cloud-computing-dictionary/what-is-natural-language-processing-nlp

## Additional Technical Reading

- Statistical Language Modeling overview:
  https://www.microsoft.com/en-us/research/wp-content/uploads/2017/01/Introduction-to-the-Special-Issue-on-Statistical-Language-Modeling.pdf

- https://azure.microsoft.com/resources/cloud-computing-dictionary/what-is-natural-language-processing-nlp
- https://azure.microsoft.com/products/ai-foundry/tools/language
