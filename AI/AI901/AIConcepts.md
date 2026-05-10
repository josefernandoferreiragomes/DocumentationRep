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
