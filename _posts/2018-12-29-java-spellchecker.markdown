---
layout: post
title:  "Implementing a Spellchecker on Java"
date:   2018-12-29 12:00:00 +0000
categories: projects
tags: java algorithms data-structures
---

[GitHub Repo](https://github.com/gokhj/Java-Spellchecker)

This is a spellchecker application implemented with Java.

What I like about programming is, you can get to a point with almost infinite ways. For this occasion, I could use tens of data structures or hundreds of algorithms.

I am using ArrayLists and Hashtables in this project along with Soundex and Levenshtein algorithm implementations from scratch.

The purpose of ArrayLists are to store the dictionary in the memory, so it will be faster to extract the desired word compared to finding it in a database. On the other hand, HashTable structure is used to store the Soundex values of each word so we can, again, see the relevant points quickly. [What is Random Access Memory?](https://www.interviewcake.com/article/python3/data-structures-coding-interview?course=fc1&section=algorithmic-thinking#ram)

### Soundex

So what is this algorithm? According to Wikipedia:

*"Soundex is a phonetic algorithm for indexing names by sound, as pronounced in English. The goal is for homophones to be encoded to the same representation so that they can be matched despite minor differences in spelling. The algorithm mainly encodes consonants; a vowel will not be encoded unless it is the first letter. Soundex is the most widely known of all phonetic algorithms (in part because it is a standard feature of popular database software such as DB2, PostgreSQL, MySQL, SQLite, Ingres, MS SQL Server and Oracle.) Improvements to Soundex are the basis for many modern phonetic algorithms."* [Source](https://en.wikipedia.org/wiki/Soundex)

In summary, the Soundex algorithm takes the string and divides into letters and replaces them with numbers.

```
b, f, p, v → 1
c, g, j, k, q, s, x, z → 2
d, t → 3
l → 4
m, n → 5
r → 6
````

This way, the algorithm classifies the word by their sound. For instance, Robert and Rubert return the same code R163, because they sound nearly identical. And if you think about a similar name, Rubin returns R150. Although it is close, you can see that if a person writes Rubert, it would have a higher chance that they meant Robert.

### Levenshtein Distance

The Levenshtein distance (a.k.a edit distance) is a measure of similarity between two strings. According to the Wikipedia:

*"In information theory, linguistics and computer science, the Levenshtein distance is a string metric for measuring the difference between two sequences. Informally, the Levenshtein distance between two words is the minimum number of single-character edits (insertions, deletions or substitutions) required to change one word into the other. It is named after the Soviet mathematician Vladimir Levenshtein, who considered this distance in 1965."* [Source](https://en.wikipedia.org/wiki/Levenshtein_distance)

The edit distance is impressive when you want to build a primitive spellchecker. It is one of the easiest ways to implement "Do you mean xxx?" feature just like Google Search.

![Equation of Edit Distance](/assets/spellchecker/equation.jpg)


### Code

``Spellchecker.java`` class includes both Soundex and Levenshtein algorithms for the user. Soundex Algorithm can be inspected at ``lines 108-171``. Levenshtein Distance can be seen at ``lines 180-217``.

``SpellcheckerSuggestion.java`` class, on the other hand, is relatively shorter but extends ``Spellchecker`` class to work, that means it's a more advanced version. It mainly uses the Soundex Algorithm to match the words inside the dictionary file.

The user can change the edit distance (Levenshtein) on ``line 52``. The default distance is 2, and increasing it will get the user more suggestions.

Thanks for reading.