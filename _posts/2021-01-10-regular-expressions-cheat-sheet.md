---
layout: post
title: Regular expressions cheat sheet
---

Regular expressions are a sequence of characters that define a search pattern used to perform operations on strings. Once
you’ve learned the basic syntax you can use this tool in almost all programming languages.

That said there are a variety of syntaxes such as the POSIX standard and perl compatible regular expressions (PCRE). To
confuse things more the POSIX standard splits implementations into two kinds, basic regular expressions (BRE) and
extended regular expressions (ERE) with the key difference being the behavior of a few special characters.
Python’s `re` module adopts syntax similar to Perl’s.

Throughout my career I found myself having to write regular expressions for various tasks, although I never really took
the time to fully absorb the language and would find myself searching online for solutions or a reminder of the syntax.
So as to consolidate my understanding once and for all I wrote myself this cheat sheet.

## Character classes
|    | Description                   |
|----|-------------------------------|
| `.`  | any character except new line |
| `\w` | word                          |
| `\W` | not word                      |
| `\d` | digit                         |
| `\D` | not digit                     |
| `\s` | white space                   |
| `\S` | not white space               |

## Anchors
|    | Description                   |
|----|-------------------------------|
| `^`  | beginning of line             |
| `\A` | beginning of input            |
| `$`  | end of line                   |
| `\Z` | end of input                  |
| `\b` | a word boundary               |
| `\B` | a non word boundary           |

## Quantifiers
|       | Description                                   |
|-------|-----------------------------------------------|
| `?`     | 0 or once                                     |
| `*`     | 0 or more                                     |
| `+`     | 1 or more                                     |
| `{n}`   | exactly n                                     |
| `{n,}`  | n or more                                     |
| `{n,m}` | between n and m                               |
| `x?`    | add ? to a quantifier to make it not greedy   |

## Groups and ranges
|               | Description                           |
|---------------|---------------------------------------|
| `a|b`           | a or b                                |
| `[abc]`         | a, b or c                             |
| `[^abc]`        | not (a or b or c)                     |
| `[a-q]`         | letters from a to q                   |
| `[0-7]`         | digits from 0 to 7                    |
| `()`            | group                                 |
| `(?:regex)`     | non capturing group                   |
| `(?<name>)`     | name a group                          |
| `\n`            | refers to matched group starting at 1 |
| `(?P=name)`     | back reference to a named group       |

## Assertions
|                                       | Description           |
|---------------------------------------|-----------------------|
| `(?=regex)`                           | lookahead positive    |
| `(?!regex)`                           | lookahead negative    |
| `(?<=regex)`                          | lookbehind positive   |
| `(?<!regex)`                          | lookbehind negative   |
| `(?(<n>)<yes-regex>|<no-regex>)`      | group number          |
| `(?(<name>)<yes-regex>|<no-regex>)`   | group name            |


By creating this cheat sheet my hope is to have consolidated my understanding of regular expression. While researching it was fun going a little deeper on some of the more advanced features such as lookaheads which definitely gave me a renewed appreciation of what is possible with the language.
