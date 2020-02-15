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

<!-- more -->

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

lemmatizer = WordNetLemmatizer()

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

    [nltk_data] Downloading package stopwords to
    [nltk_data]     C:\Users\VAL\AppData\Roaming\nltk_data...
    [nltk_data]   Package stopwords is already up-to-date!
    




    True




```python
from nltk.corpus import stopwords

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



## Parts of speech tag

標註詞性，為了讓詞性標註更準確，不要使用stemming、lemmatize、lower處理過的內容，而是使用原文


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




```python
tagged_words = nltk.pos_tag(words)
tagged_words
```




    [('An', 'DT'),
     ('aircraft', 'NN'),
     ('is', 'VBZ'),
     ('a', 'DT'),
     ('vehicle', 'NN'),
     ('that', 'WDT'),
     ('is', 'VBZ'),
     ('able', 'JJ'),
     ('to', 'TO'),
     ('fly', 'VB'),
     ('by', 'IN'),
     ('gaining', 'VBG'),
     ('support', 'NN'),
     ('from', 'IN'),
     ('the', 'DT'),
     ('air', 'NN'),
     ('.', '.'),
     ('It', 'PRP'),
     ('counters', 'VBZ'),
     ('the', 'DT'),
     ('force', 'NN'),
     ('of', 'IN'),
     ('gravity', 'NN'),
     ('by', 'IN'),
     ('using', 'VBG'),
     ('either', 'CC'),
     ('static', 'JJ'),
     ('lift', 'NN'),
     ('or', 'CC'),
     ('by', 'IN'),
     ('using', 'VBG'),
     ('the', 'DT'),
     ('dynamic', 'JJ'),
     ('lift', 'NN'),
     ('of', 'IN'),
     ('an', 'DT'),
     ('airfoil', 'NN'),
     (',', ','),
     ('or', 'CC'),
     ('in', 'IN'),
     ('a', 'DT'),
     ('few', 'JJ'),
     ('cases', 'NNS'),
     ('the', 'DT'),
     ('downward', 'JJ'),
     ('thrust', 'NN'),
     ('from', 'IN'),
     ('jet', 'NN'),
     ('engines', 'NNS'),
     ('.', '.'),
     ('Common', 'NNP'),
     ('examples', 'NNS'),
     ('of', 'IN'),
     ('aircraft', 'NN'),
     ('include', 'VBP'),
     ('airplanes', 'NNS'),
     (',', ','),
     ('helicopters', 'NNS'),
     (',', ','),
     ('airships', 'NNS'),
     ('(', '('),
     ('including', 'VBG'),
     ('blimps', 'NNS'),
     (')', ')'),
     (',', ','),
     ('gliders', 'NNS'),
     (',', ','),
     ('paramotors', 'NNS'),
     ('and', 'CC'),
     ('hot', 'JJ'),
     ('air', 'NN'),
     ('balloons', 'NNS'),
     ('.', '.'),
     ('The', 'DT'),
     ('human', 'JJ'),
     ('activity', 'NN'),
     ('that', 'IN'),
     ('surrounds', 'VBZ'),
     ('aircraft', 'NN'),
     ('is', 'VBZ'),
     ('called', 'VBN'),
     ('aviation', 'NN'),
     ('.', '.'),
     ('The', 'DT'),
     ('science', 'NN'),
     ('of', 'IN'),
     ('aviation', 'NN'),
     (',', ','),
     ('including', 'VBG'),
     ('designing', 'NN'),
     ('and', 'CC'),
     ('building', 'NN'),
     ('aircraft', 'NN'),
     (',', ','),
     ('is', 'VBZ'),
     ('called', 'VBN'),
     ('aeronautics', 'NNS'),
     ('.', '.'),
     ('Crewed', 'VBN'),
     ('aircraft', 'NN'),
     ('are', 'VBP'),
     ('flown', 'VBN'),
     ('by', 'IN'),
     ('an', 'DT'),
     ('onboard', 'NN'),
     ('pilot', 'NN'),
     (',', ','),
     ('but', 'CC'),
     ('unmanned', 'JJ'),
     ('aerial', 'JJ'),
     ('vehicles', 'NNS'),
     ('may', 'MD'),
     ('be', 'VB'),
     ('remotely', 'RB'),
     ('controlled', 'VBN'),
     ('or', 'CC'),
     ('self-controlled', 'VBN'),
     ('by', 'IN'),
     ('onboard', 'JJ'),
     ('computers', 'NNS'),
     ('.', '.'),
     ('Aircraft', 'NN'),
     ('may', 'MD'),
     ('be', 'VB'),
     ('classified', 'VBN'),
     ('by', 'IN'),
     ('different', 'JJ'),
     ('criteria', 'NNS'),
     (',', ','),
     ('such', 'JJ'),
     ('as', 'IN'),
     ('lift', 'NN'),
     ('type', 'NN'),
     (',', ','),
     ('aircraft', 'NN'),
     ('propulsion', 'NN'),
     (',', ','),
     ('usage', 'NN'),
     ('and', 'CC'),
     ('others', 'NNS'),
     ('.', '.')]




```python
word_tags = []

for tw in tagged_words:
    word_tags.append(tw[0] + "_" + tw[1])
    
word_tags
```




    ['An_DT',
     'aircraft_NN',
     'is_VBZ',
     'a_DT',
     'vehicle_NN',
     'that_WDT',
     'is_VBZ',
     'able_JJ',
     'to_TO',
     'fly_VB',
     'by_IN',
     'gaining_VBG',
     'support_NN',
     'from_IN',
     'the_DT',
     'air_NN',
     '._.',
     'It_PRP',
     'counters_VBZ',
     'the_DT',
     'force_NN',
     'of_IN',
     'gravity_NN',
     'by_IN',
     'using_VBG',
     'either_CC',
     'static_JJ',
     'lift_NN',
     'or_CC',
     'by_IN',
     'using_VBG',
     'the_DT',
     'dynamic_JJ',
     'lift_NN',
     'of_IN',
     'an_DT',
     'airfoil_NN',
     ',_,',
     'or_CC',
     'in_IN',
     'a_DT',
     'few_JJ',
     'cases_NNS',
     'the_DT',
     'downward_JJ',
     'thrust_NN',
     'from_IN',
     'jet_NN',
     'engines_NNS',
     '._.',
     'Common_NNP',
     'examples_NNS',
     'of_IN',
     'aircraft_NN',
     'include_VBP',
     'airplanes_NNS',
     ',_,',
     'helicopters_NNS',
     ',_,',
     'airships_NNS',
     '(_(',
     'including_VBG',
     'blimps_NNS',
     ')_)',
     ',_,',
     'gliders_NNS',
     ',_,',
     'paramotors_NNS',
     'and_CC',
     'hot_JJ',
     'air_NN',
     'balloons_NNS',
     '._.',
     'The_DT',
     'human_JJ',
     'activity_NN',
     'that_IN',
     'surrounds_VBZ',
     'aircraft_NN',
     'is_VBZ',
     'called_VBN',
     'aviation_NN',
     '._.',
     'The_DT',
     'science_NN',
     'of_IN',
     'aviation_NN',
     ',_,',
     'including_VBG',
     'designing_NN',
     'and_CC',
     'building_NN',
     'aircraft_NN',
     ',_,',
     'is_VBZ',
     'called_VBN',
     'aeronautics_NNS',
     '._.',
     'Crewed_VBN',
     'aircraft_NN',
     'are_VBP',
     'flown_VBN',
     'by_IN',
     'an_DT',
     'onboard_NN',
     'pilot_NN',
     ',_,',
     'but_CC',
     'unmanned_JJ',
     'aerial_JJ',
     'vehicles_NNS',
     'may_MD',
     'be_VB',
     'remotely_RB',
     'controlled_VBN',
     'or_CC',
     'self-controlled_VBN',
     'by_IN',
     'onboard_JJ',
     'computers_NNS',
     '._.',
     'Aircraft_NN',
     'may_MD',
     'be_VB',
     'classified_VBN',
     'by_IN',
     'different_JJ',
     'criteria_NNS',
     ',_,',
     'such_JJ',
     'as_IN',
     'lift_NN',
     'type_NN',
     ',_,',
     'aircraft_NN',
     'propulsion_NN',
     ',_,',
     'usage_NN',
     'and_CC',
     'others_NNS',
     '._.']




```python
tagged_paragraph = ' '.join(word_tags)

tagged_paragraph
```




    'An_DT aircraft_NN is_VBZ a_DT vehicle_NN that_WDT is_VBZ able_JJ to_TO fly_VB by_IN gaining_VBG support_NN from_IN the_DT air_NN ._. It_PRP counters_VBZ the_DT force_NN of_IN gravity_NN by_IN using_VBG either_CC static_JJ lift_NN or_CC by_IN using_VBG the_DT dynamic_JJ lift_NN of_IN an_DT airfoil_NN ,_, or_CC in_IN a_DT few_JJ cases_NNS the_DT downward_JJ thrust_NN from_IN jet_NN engines_NNS ._. Common_NNP examples_NNS of_IN aircraft_NN include_VBP airplanes_NNS ,_, helicopters_NNS ,_, airships_NNS (_( including_VBG blimps_NNS )_) ,_, gliders_NNS ,_, paramotors_NNS and_CC hot_JJ air_NN balloons_NNS ._. The_DT human_JJ activity_NN that_IN surrounds_VBZ aircraft_NN is_VBZ called_VBN aviation_NN ._. The_DT science_NN of_IN aviation_NN ,_, including_VBG designing_NN and_CC building_NN aircraft_NN ,_, is_VBZ called_VBN aeronautics_NNS ._. Crewed_VBN aircraft_NN are_VBP flown_VBN by_IN an_DT onboard_NN pilot_NN ,_, but_CC unmanned_JJ aerial_JJ vehicles_NNS may_MD be_VB remotely_RB controlled_VBN or_CC self-controlled_VBN by_IN onboard_JJ computers_NNS ._. Aircraft_NN may_MD be_VB classified_VBN by_IN different_JJ criteria_NNS ,_, such_JJ as_IN lift_NN type_NN ,_, aircraft_NN propulsion_NN ,_, usage_NN and_CC others_NNS ._.'



> tags所代表得意思可參考這個[網頁](https://pythonprogramming.net/natural-language-toolkit-nltk-part-speech-tagging/)

## Named Entity Recognition

專有名詞辨識


```python
paragraph = "Apple CEO Tim Cook introduces iPhones and smart watch at Cupertino Flint Center event."

words= nltk.word_tokenize(paragraph)

tagged_words = nltk.pos_tag(words)

tagged_words
```




    [('Apple', 'NNP'),
     ('CEO', 'NNP'),
     ('Tim', 'NNP'),
     ('Cook', 'NNP'),
     ('introduces', 'VBZ'),
     ('iPhones', 'NNS'),
     ('and', 'CC'),
     ('smart', 'JJ'),
     ('watch', 'NN'),
     ('at', 'IN'),
     ('Cupertino', 'NNP'),
     ('Flint', 'NNP'),
     ('Center', 'NNP'),
     ('event', 'NN'),
     ('.', '.')]




```python
named_entity = nltk.ne_chunk(tagged_words)

named_entity.draw()
```

## Bag-of-words model

所有單字權重相同，沒有保留語義資訊


```python
paragraph = """An    aircraft is a vehicle that is able to fly by gaining support from the air. It counters the force of gravity by using either static lift or by using the dynamic lift of an airfoil, or in a few cases the downward thrust from jet engines. Common examples of aircraft include airplanes, helicopters, airships (including blimps), gliders, paramotors and hot air balloons. The human activity that surrounds aircraft is called aviation. The science of aviation, including designing and building aircraft, is called aeronautics. Crewed aircraft are flown by an onboard pilot, but unmanned aerial vehicles may be remotely controlled or self-controlled by onboard computers. Aircraft may be classified   by different criteria, such as lift type, aircraft propulsion, usage and others."""

import re

dataset = nltk.sent_tokenize(paragraph)
```

每個句子中的字母改成小寫，用一個空白取代標點符號以及多個空白


```python
for i in range(len(dataset)):
    dataset[i] = dataset[i].lower()
    dataset[i] = re.sub(r'\W', ' ', dataset[i]) # substitute all the non word characters with a space
    dataset[i] = re.sub(r'\s+', ' ', dataset[i]) # trim spaces
    
dataset
```




    ['an aircraft is a vehicle that is able to fly by gaining support from the air ',
     'it counters the force of gravity by using either static lift or by using the dynamic lift of an airfoil or in a few cases the downward thrust from jet engines ',
     'common examples of aircraft include airplanes helicopters airships including blimps gliders paramotors and hot air balloons ',
     'the human activity that surrounds aircraft is called aviation ',
     'the science of aviation including designing and building aircraft is called aeronautics ',
     'crewed aircraft are flown by an onboard pilot but unmanned aerial vehicles may be remotely controlled or self controlled by onboard computers ',
     'aircraft may be classified by different criteria such as lift type aircraft propulsion usage and others ']




```python
word2count = {}

for data in dataset:
    words = nltk.word_tokenize(data)
    for word in words:
        if word not in word2count.keys():
            word2count[word] = 1
        else:
            word2count[word] += 1
            
word2count
```




    {'an': 3,
     'aircraft': 7,
     'is': 4,
     'a': 2,
     'vehicle': 1,
     'that': 2,
     'able': 1,
     'to': 1,
     'fly': 1,
     'by': 6,
     'gaining': 1,
     'support': 1,
     'from': 2,
     'the': 6,
     'air': 2,
     'it': 1,
     'counters': 1,
     'force': 1,
     'of': 4,
     'gravity': 1,
     'using': 2,
     'either': 1,
     'static': 1,
     'lift': 3,
     'or': 3,
     'dynamic': 1,
     'airfoil': 1,
     'in': 1,
     'few': 1,
     'cases': 1,
     'downward': 1,
     'thrust': 1,
     'jet': 1,
     'engines': 1,
     'common': 1,
     'examples': 1,
     'include': 1,
     'airplanes': 1,
     'helicopters': 1,
     'airships': 1,
     'including': 2,
     'blimps': 1,
     'gliders': 1,
     'paramotors': 1,
     'and': 3,
     'hot': 1,
     'balloons': 1,
     'human': 1,
     'activity': 1,
     'surrounds': 1,
     'called': 2,
     'aviation': 2,
     'science': 1,
     'designing': 1,
     'building': 1,
     'aeronautics': 1,
     'crewed': 1,
     'are': 1,
     'flown': 1,
     'onboard': 2,
     'pilot': 1,
     'but': 1,
     'unmanned': 1,
     'aerial': 1,
     'vehicles': 1,
     'may': 2,
     'be': 2,
     'remotely': 1,
     'controlled': 2,
     'self': 1,
     'computers': 1,
     'classified': 1,
     'different': 1,
     'criteria': 1,
     'such': 1,
     'as': 1,
     'type': 1,
     'propulsion': 1,
     'usage': 1,
     'others': 1}




```python
import heapq

freq_words = heapq.nlargest(10, word2count, key=word2count.get) # Find the 100 largest elements in a dataset.

x = []

for data in dataset:
    vector = []
    for word in freq_words:
        if word in nltk.word_tokenize(data):
            vector.append(1)
        else:
            vector.append(0)
    x.append(vector)
    
import numpy as np

x = np.asanyarray(x)
x
```




    array([[1, 1, 1, 1, 0, 1, 0, 0, 0, 1],
           [0, 1, 1, 0, 1, 1, 1, 1, 0, 1],
           [1, 0, 0, 0, 1, 0, 0, 0, 1, 0],
           [1, 0, 1, 1, 0, 0, 0, 0, 0, 0],
           [1, 0, 1, 1, 1, 0, 0, 0, 1, 0],
           [1, 1, 0, 0, 0, 1, 0, 1, 0, 0],
           [1, 1, 0, 0, 0, 0, 1, 0, 1, 0]])



## TF-IDF model

單字的重要性單純根據出現次數不太合理，tf-idf是一種統計方法，用以評估一字詞對於一個檔案集或一個語料庫中的其中一份檔案的重要程度。字詞的重要性隨著它在檔案中出現的次數成正比增加，但同時會隨著它在語料庫中出現的頻率成反比下降。

* TF = Term Frequency 
    * 單一文件中，某單字字數除以文件單字數
* IDF = Inverse Document Frequency 
    * 文件數除以包含該單字的文件數之後取對數
* TF-IDF = TF * IDF

有個三份文件(sentence)

文件1："to be or not to be"

文件2："i have to be"

文件3："you got to be"

文件1中的TF計算方式如下

$to = \frac{2}{6} = 0.33$ 

$be = \frac{2}{6} = 0.33$

$or = \frac{1}{6} = 0.16$



| 文件1 |to | be| or|not| 
|---|---|---|---|---|
|TF|0.33|0.33|0.16|0.16|

| 文件2 |i | have| to|be| 
|---|---|---|---|---|
|TF|0.25|0.25|0.25|0.25|

| 文件3 | you | got| to|be| 
|---|---|---|---|---|
|TF|0.25|0.25|0.25|0.25|

IDF計算方式如下，

$to = log(\frac{3}{3}) = 0$

$be = log(\frac{3}{3}) = 0$

$have = log(\frac{3}{1})$

|  - | to | be | or | not | i | have | you | got |
|---|---|---|---|---|---|---|---|---|
| -  |3/3|3/3|3/1|3/1|3/1|3/1|3/1|3/1|
|IDF| 0  |0  | 0.47 | 0.47 | 0.47 | 0.47 | 0.47 | 0.47 |

依此類推，最後可列出下表

$\text{TF-IDF}(word) = \text{TF}(Document, word) \times \text{IDF}(word)$

| TF-IDF |to | be| or|not| i |have|you|got|
|---|---|---|---|---|---|---|---|---|
|文件1|0 |0  |0.0752|0.0752|0|0|0|0|
|文件2|0 |0  |0|0|0.1175|0.1175|0|0|
|文件3|0 |0  | 0|0|0|0|0.1175|0.1175|


```python
word_idfs = {}

for word in freq_words:
    doc_count = 0
    for data in dataset:
        if word in nltk.word_tokenize(data):
            doc_count += 1
    word_idfs[word] = np.log(1 + (len(dataset) / doc_count)) # 如果詞語不在資料中，就導致分母為零，因此+1作為bias
    
word_idfs
```




    {'aircraft': 0.7731898882334819,
     'by': 1.0116009116784799,
     'the': 1.0116009116784799,
     'is': 1.2039728043259361,
     'of': 1.2039728043259361,
     'an': 1.2039728043259361,
     'lift': 1.5040773967762742,
     'or': 1.5040773967762742,
     'and': 1.2039728043259361,
     'a': 1.5040773967762742}



## TF Matrix


```python
tf_matrix = {}

for word in freq_words:
    doc_tf = []
    for data in dataset:
        frequency = 0
        for w in nltk.word_tokenize(data):
            if w == word:
                frequency += 1
        tf_word = frequency / len(nltk.word_tokenize(data))
        doc_tf.append(tf_word)
    tf_matrix[word] = doc_tf
    
tf_matrix
```




    {'aircraft': [0.0625,
      0.0,
      0.0625,
      0.1111111111111111,
      0.08333333333333333,
      0.045454545454545456,
      0.125],
     'by': [0.0625,
      0.06451612903225806,
      0.0,
      0.0,
      0.0,
      0.09090909090909091,
      0.0625],
     'the': [0.0625,
      0.0967741935483871,
      0.0,
      0.1111111111111111,
      0.08333333333333333,
      0.0,
      0.0],
     'is': [0.125, 0.0, 0.0, 0.1111111111111111, 0.08333333333333333, 0.0, 0.0],
     'of': [0.0, 0.06451612903225806, 0.0625, 0.0, 0.08333333333333333, 0.0, 0.0],
     'an': [0.0625, 0.03225806451612903, 0.0, 0.0, 0.0, 0.045454545454545456, 0.0],
     'lift': [0.0, 0.06451612903225806, 0.0, 0.0, 0.0, 0.0, 0.0625],
     'or': [0.0, 0.06451612903225806, 0.0, 0.0, 0.0, 0.045454545454545456, 0.0],
     'and': [0.0, 0.0, 0.0625, 0.0, 0.08333333333333333, 0.0, 0.0625],
     'a': [0.0625, 0.03225806451612903, 0.0, 0.0, 0.0, 0.0, 0.0]}



## TF-IDF Calculation


```python
tfidf_matrix = []

for word in tf_matrix.keys():
    tfidf = []
    for value in tf_matrix[word]:
        score = value * word_idfs[word]
        tfidf.append(score)
    tfidf_matrix.append(tfidf)
    
tfidf_matrix
```




    [[0.04832436801459262,
      0.0,
      0.04832436801459262,
      0.08590998758149798,
      0.06443249068612349,
      0.03514499491970372,
      0.09664873602918524],
     [0.06322505697990499,
      0.0652645749469987,
      0.0,
      0.0,
      0.0,
      0.09196371924349818,
      0.06322505697990499],
     [0.06322505697990499,
      0.09789686242049805,
      0.0,
      0.11240010129760887,
      0.08430007597320666,
      0.0,
      0.0],
     [0.15049660054074201,
      0.0,
      0.0,
      0.1337747560362151,
      0.10033106702716134,
      0.0,
      0.0],
     [0.0,
      0.07767566479522169,
      0.07524830027037101,
      0.0,
      0.10033106702716134,
      0.0,
      0.0],
     [0.07524830027037101,
      0.03883783239761084,
      0.0,
      0.0,
      0.0,
      0.05472603656026982,
      0.0],
     [0.0, 0.09703725140492091, 0.0, 0.0, 0.0, 0.0, 0.09400483729851714],
     [0.0, 0.09703725140492091, 0.0, 0.0, 0.0, 0.06836715439892156, 0.0],
     [0.0,
      0.0,
      0.07524830027037101,
      0.0,
      0.10033106702716134,
      0.0,
      0.07524830027037101],
     [0.09400483729851714, 0.048518625702460454, 0.0, 0.0, 0.0, 0.0, 0.0]]




```python
np.transpose(np.asarray(tfidf_matrix))
```




    array([[0.04832437, 0.06322506, 0.06322506, 0.1504966 , 0.        ,
            0.0752483 , 0.        , 0.        , 0.        , 0.09400484],
           [0.        , 0.06526457, 0.09789686, 0.        , 0.07767566,
            0.03883783, 0.09703725, 0.09703725, 0.        , 0.04851863],
           [0.04832437, 0.        , 0.        , 0.        , 0.0752483 ,
            0.        , 0.        , 0.        , 0.0752483 , 0.        ],
           [0.08590999, 0.        , 0.1124001 , 0.13377476, 0.        ,
            0.        , 0.        , 0.        , 0.        , 0.        ],
           [0.06443249, 0.        , 0.08430008, 0.10033107, 0.10033107,
            0.        , 0.        , 0.        , 0.10033107, 0.        ],
           [0.03514499, 0.09196372, 0.        , 0.        , 0.        ,
            0.05472604, 0.        , 0.06836715, 0.        , 0.        ],
           [0.09664874, 0.06322506, 0.        , 0.        , 0.        ,
            0.        , 0.09400484, 0.        , 0.0752483 , 0.        ]])

