---
title: Python - NLTK函式庫基本功能介紹與自然語言處理
date: 2019-09-08 17:23:16
tags:
- NLP
- NLTK
- Python
categories:
- Miscellaneous
thumbnail: https://i.imgur.com/dGDBend.png
---



```python
import nltk
# nltk.download()
```

conda安裝nltk後引入函式庫，第一次使用可由`nltk.download()`下載所需檔案

## Tokenization


```python
paragraph = """An aircraft is a vehicle that is able to fly by gaining support from the air. It counters the force of gravity by using either static lift or by using the dynamic lift of an airfoil, or in a few cases the downward thrust from jet engines. Common examples of aircraft include airplanes, helicopters, airships (including blimps), gliders, paramotors and hot air balloons. The human activity that surrounds aircraft is called aviation. The science of aviation, including designing and building aircraft, is called aeronautics. Crewed aircraft are flown by an onboard pilot, but unmanned aerial vehicles may be remotely controlled or self-controlled by onboard computers. Aircraft may be classified by different criteria, such as lift type, aircraft propulsion, usage and others."""
```

Tokenization是指將整份文件分離出單字、句子、片語等等

<!-- more -->

```python
nltk.sent_tokenize(paragraph)
```




    ['An aircraft is a vehicle that is able to fly by gaining support from the air.',
     'It counters the force of gravity by using either static lift or by using the dynamic lift of an airfoil, or in a few cases the downward thrust from jet engines.',
     'Common examples of aircraft include airplanes, helicopters, airships (including blimps), gliders, paramotors and hot air balloons.',
     'The human activity that surrounds aircraft is called aviation.',
     'The science of aviation, including designing and building aircraft, is called aeronautics.',
     'Crewed aircraft are flown by an onboard pilot, but unmanned aerial vehicles may be remotely controlled or self-controlled by onboard computers.',
     'Aircraft may be classified by different criteria, such as lift type, aircraft propulsion, usage and others.']



一般對文章做word tokenization，可以用python的`split()`做到

```py
str = "I am Lin"
words = str.split(" ")

# Out: ['I', 'am', 'Lin']
```

nltk則提供`nltk.word_tokenize()`


```python
words = nltk.word_tokenize(paragraph)
words
```




    ['An',
     'aircraft',
     'is',
     'a',
     'vehicle',
     'that',
     'is',
     'able',
     'to',
     'fly',
     'by',
     'gaining',
     'support',
     'from',
     'the',
     'air',
     '.',
     'It',
     'counters',
     'the',
     'force',
     'of',
     'gravity',
     'by',
     'using',
     'either',
     'static',
     'lift',
     'or',
     'by',
     'using',
     'the',
     'dynamic',
     'lift',
     'of',
     'an',
     'airfoil',
     ',',
     'or',
     'in',
     'a',
     'few',
     'cases',
     'the',
     'downward',
     'thrust',
     'from',
     'jet',
     'engines',
     '.',
     'Common',
     'examples',
     'of',
     'aircraft',
     'include',
     'airplanes',
     ',',
     'helicopters',
     ',',
     'airships',
     '(',
     'including',
     'blimps',
     ')',
     ',',
     'gliders',
     ',',
     'paramotors',
     'and',
     'hot',
     'air',
     'balloons',
     '.',
     'The',
     'human',
     'activity',
     'that',
     'surrounds',
     'aircraft',
     'is',
     'called',
     'aviation',
     '.',
     'The',
     'science',
     'of',
     'aviation',
     ',',
     'including',
     'designing',
     'and',
     'building',
     'aircraft',
     ',',
     'is',
     'called',
     'aeronautics',
     '.',
     'Crewed',
     'aircraft',
     'are',
     'flown',
     'by',
     'an',
     'onboard',
     'pilot',
     ',',
     'but',
     'unmanned',
     'aerial',
     'vehicles',
     'may',
     'be',
     'remotely',
     'controlled',
     'or',
     'self-controlled',
     'by',
     'onboard',
     'computers',
     '.',
     'Aircraft',
     'may',
     'be',
     'classified',
     'by',
     'different',
     'criteria',
     ',',
     'such',
     'as',
     'lift',
     'type',
     ',',
     'aircraft',
     'propulsion',
     ',',
     'usage',
     'and',
     'others',
     '.']



## Stemming & Lemmatization

**Stemming**是指將不同時態或是不同變化的詞縮減成一個單字，例如intelligence, intelligent, intelligently的stemming都是intelligen，
going, goes, gone都是go，**lemmatization**則是字根化，就是把字還原到字根的型態，保留字義，例如intelligence, intelligent, intelligently是intelligent

因此lemmatization過程花費時間往往較長，也因為提取單字原意對於分析上較有幫助，常用在如question answering等等應用上。而對於字義影響相對不大的應用，如spam detection，則會用stemming提高效能。


```python
from nltk.stem import PorterStemmer
```


```python
sentences = nltk.sent_tokenize(paragraph)
sentences
```




    ['An aircraft is a vehicle that is able to fly by gaining support from the air.',
     'It counters the force of gravity by using either static lift or by using the dynamic lift of an airfoil, or in a few cases the downward thrust from jet engines.',
     'Common examples of aircraft include airplanes, helicopters, airships (including blimps), gliders, paramotors and hot air balloons.',
     'The human activity that surrounds aircraft is called aviation.',
     'The science of aviation, including designing and building aircraft, is called aeronautics.',
     'Crewed aircraft are flown by an onboard pilot, but unmanned aerial vehicles may be remotely controlled or self-controlled by onboard computers.',
     'Aircraft may be classified by different criteria, such as lift type, aircraft propulsion, usage and others.']




```python
stemmer = PorterStemmer()
```


```python
sentences_stem = []

for i in range(len(sentences)):
    words = nltk.word_tokenize(sentences[i])
    words_stem = [stemmer.stem(word) for word in words]
    sentences_stem.append(' '.join(words_stem))
    
sentences_stem
```




    ['An aircraft is a vehicl that is abl to fli by gain support from the air .',
     'It counter the forc of graviti by use either static lift or by use the dynam lift of an airfoil , or in a few case the downward thrust from jet engin .',
     'common exampl of aircraft includ airplan , helicopt , airship ( includ blimp ) , glider , paramotor and hot air balloon .',
     'the human activ that surround aircraft is call aviat .',
     'the scienc of aviat , includ design and build aircraft , is call aeronaut .',
     'crew aircraft are flown by an onboard pilot , but unman aerial vehicl may be remot control or self-control by onboard comput .',
     'aircraft may be classifi by differ criteria , such as lift type , aircraft propuls , usag and other .']



可以看到stemming會直接縮減單字，如vehicle改成了vehicl

接下來用**Lemmatization** api處理文章


```python
from nltk.stem import WordNetLemmatizer
```


```python
lemmatizer = WordNetLemmatizer()
```


```python
sentences_lem = []

for i in range(len(sentences)):
    words = nltk.word_tokenize(sentences[i])
    words_lem = [lemmatizer.lemmatize(word) for word in words]
    sentences_lem.append(' '.join(words_lem))
    
sentences_lem
```




    ['An aircraft is a vehicle that is able to fly by gaining support from the air .',
     'It counter the force of gravity by using either static lift or by using the dynamic lift of an airfoil , or in a few case the downward thrust from jet engine .',
     'Common example of aircraft include airplane , helicopter , airship ( including blimp ) , glider , paramotors and hot air balloon .',
     'The human activity that surround aircraft is called aviation .',
     'The science of aviation , including designing and building aircraft , is called aeronautics .',
     'Crewed aircraft are flown by an onboard pilot , but unmanned aerial vehicle may be remotely controlled or self-controlled by onboard computer .',
     'Aircraft may be classified by different criterion , such a lift type , aircraft propulsion , usage and others .']



## Stop word removal

英文中有很多字對於句子的理解沒有影響，例如連接詞、be動詞等等，例如"I am going to station."只要有"I go station"其實就包含足夠意義。第一次使用可由`nltk.download('stopwords')`下載詞庫


```python
nltk.download('stopwords')
```

    [nltk_data] Downloading package stopwords to /home/yc/nltk_data...
    [nltk_data]   Package stopwords is already up-to-date!





    True




```python
from nltk.corpus import stopwords
```


```python
sentences_wo_stop = []

for i in range(len(sentences)):
    words = nltk.word_tokenize(sentences[i])
    words_wo_stop = [word for word in words if word not in stopwords.words('english')]
    sentences_wo_stop.append(' '.join(words_wo_stop))
    
sentences_wo_stop
```




    ['An aircraft vehicle able fly gaining support air .',
     'It counters force gravity using either static lift using dynamic lift airfoil , cases downward thrust jet engines .',
     'Common examples aircraft include airplanes , helicopters , airships ( including blimps ) , gliders , paramotors hot air balloons .',
     'The human activity surrounds aircraft called aviation .',
     'The science aviation , including designing building aircraft , called aeronautics .',
     'Crewed aircraft flown onboard pilot , unmanned aerial vehicles may remotely controlled self-controlled onboard computers .',
     'Aircraft may classified different criteria , lift type , aircraft propulsion , usage others .']




```python

```
