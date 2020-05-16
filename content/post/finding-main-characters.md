---
title: Finding the main characters in a novel
subtitle: A method to find important characters in a story or novel
date: 2019-10-31
# bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "Python", "NER", "NLP" ]
---

Find the code [`here`](https://colab.research.google.com/drive/10lrWpC3BF2Lan7LHrNZXs6H0oqTZW19_) 

<!--more-->
 
# Introduction
In this tutorial we'll be learning how to extract the names of all the main characters from a book.
 
 
# Setup
All the code for this tutorial will be executed on a [`Google Colab notebook`](https://colab.research.google.com). Go to the link and create a new Python 3 notebook and change the runtime type to GPU.
 
# Strategy
The novel we'll be using can be any book from the [`Gutenberg Project`](https://www.gutenberg.org/browse/scores/top#books-last30) in raw UTF-8 format. To extract the names of all the main characters first we need to remove all `\n` and `\r` characters from the raw text.
 
To identify all characters, we'll be using Named Entity Recognition. A named entity is a real world object, like a place, person, organisation, etc. 
 
The text needs to be tokenized into sentences and all the named entities in those sentences need to be identified. We'll only be looking at entities tagged as people because we're only interested in the characters.
 
Then these characters can be listed in the order of their frequency of occurrence in the text.
 
# Implementation
 
For named entity recognition we'll use a library called [`flair`](https://github.com/zalandoresearch/flair). To install flair run
 
```
!pip install flair
```
Next we'll import NLTK, flair and a few other python libraries
{{< highlight python >}}
import nltk
from nltk import pos_tag, word_tokenize
from nltk.tokenize import sent_tokenize
from nltk.corpus import stopwords
nltk.download('stopwords')
nltk.download('punkt')
nltk.download('gutenberg')
 
from tqdm import tqdm
import re
import string
from itertools import combinations
from collections import Counter
 
 
from flair.models import SequenceTagger
from flair.data import Sentence
 
{{< / highlight >}}
 
The book can be downloaded and read as a text file. NLTK already comes with a few Project Gutenberg books so we'll be using one of those for convenience
 
{{< highlight python >}}
book = nltk.corpus.gutenberg.raw('carroll-alice.txt')
{{< / highlight >}}
 
The book variable is a string that looks like this
```
Alice\'s Adventures in Wonderland by Lewis Carroll 1865]\n\nCHAPTER I. Down the Rabbit-Hole\n\nAlice was beginning to get very tired of sitting by her sister on the\nbank, and of having nothing to do: once or twice she had peeped into the\nbook her sister was reading, but it had no pictures or conversations in\nit, \'and what is the use of a book ....
```
 
The text has to be cleaned to remove some unwanted characters
{{< highlight python >}}
 
book = book.replace('\n', ' ')
book = book.replace('\r', ' ')
book = book.replace('\'', ' ')
 
{{< / highlight >}}
 
If we print book again it'll look something like this
 
```
[Alice s Adventures in Wonderland by Lewis Carroll 1865]  CHAPTER I. Down the Rabbit-Hole  Alice was beginning to get very tired of sitting by her sister on the bank, and of having nothing to do: once or twice she had peeped into the book her sister was reading, but it had no pictures or conversations in it,  and what is the use of a book ...
```
 
Removing these characters will help us extract the character names more effectively.
 
After this the book is tokenized into sentences, the flair ner tagger is loaded and each sentence is tagged one by one. If an entity is tagged as a person, it's stored in a separate array.
 
{{< highlight python >}}
# Use flair named entity recognition
tagger = SequenceTagger.load('ner')
 
# Get all the names of entities tagged as people
 
x = []
 
for line in tqdm(sent):
 sentence = Sentence(line)
 tagger.predict(sentence)
 for entity in sentence.to_dict(tag_type='ner')['entities']:
   if entity['type'] == 'PER':
     x.append(entity['text'])
{{< / highlight >}}
 
The `x` array contains all the occurences of characters mentioned in the book. Then the punctuation like commas and semicolons that might occour in the names is removed.
 
{{< highlight python >}}
# Remove any punctuation within the names
names = []
for name in x:
 names.append(name.translate(str.maketrans('', '', string.punctuation)))
{{< / highlight >}}
 
Let's sort the list based on number of times the character is mentioned in the book and print it
 
{{< highlight python >}}
# List characters by the frequency with which they are mentioned
result = [item for items, c in Counter(x).most_common()
                                     for item in [items] * c]
 
print(Counter(names).most_common())
{{< / highlight >}}
 
The output should look something like this
 
```
[('Alice', 346), ('Hatter', 52), ('Duchess', 28), ('Dormouse', 28), ('King', 28), ('Mouse', 21),
('Queen', 18), ('Dinah', 13), ... ('Lory', 4), ('William', 4), ('Mary Ann', 4), ('Well', 4), ('Cat', 4)
, ('Five', 3), ('Majesty', 3), ... ('Wow', 2), ('Treacle', 2), ('Miss', 2), ('Hush', 2), ('Yes', 2),
....]
```
 
If we look at the output closely, we can see that some of the "names", like Five, Hush, Yes, etc, don't really seem like names of people or characters. This happens because of the way that NER works, sometimes words that are probably not names of characters can also be included in the `name` list, these are generally few and can be removed manually.
 
We can also exclude characters whose names have been mentioned less than 5 times, as they aren't likely to be important characters.
 
{{< highlight python >}}
common = []
main_freq = []
# Manually remove words that are not character names from our list
 
not_names = ['Well', 'Ive', 'Five', 'Theyre', 'Dont', 'Wow',  'Ill', 'Miss', 'Hush', 'Yes',]
 
for n,c in Counter(names).most_common():
 # if the character is mentioned less than 5 times, the name is not added to the main character list
 if c > 5 and n not in not_names:
   main_freq.append((n,c))
   common.append(n)
 
{{< / highlight >}}
 
If we print the `common` array, we get a list of main characters from Alice in Wonderland
 
```
['Alice',
'Hatter',
'Duchess',
'Dormouse',
'King',
'Mouse',
'Queen',
'Dinah',
'Rabbit',
'Gryphon',
'Bill',
'Hare',
'Dodo',
'Mock Turtle',
'Footman']
```
 
# Conclusion
Although the method I've described here isn't always 100% accurate, it generally provides a satisfactory list of main characters. This list can be useful for creating a co-occurrence matrix of character interactions, which in turn can be useful for network analysis of all the characters in the book.
 
While it's beyond the scope of this post, the interaction network for all main characters can be explored in a future post.
 
<!-- {{< highlight javascript >}}
{{< / highlight >}} -->
<br>


