# Stanza Biomedical Models NLP Example

_V. Keith Hughitt_ (Aug 2020)

## Overview

In this notebook we will explore the use of pre-trained [Stanza Bio models](https://stanfordnlp.github.io/stanza/biomed.html) for analyzing text from Pubmed articles retrieved via [PubTator Central](https://www.ncbi.nlm.nih.gov/research/pubtator/api.html).

For more information, check out the pre-print on arxiv:

- [Biomedical and Clinical English Model Packages in the Stanza Python NLP Library (Zhang et al., 2020)](https://arxiv.org/abs/2007.14640)

## Setup


```python
#from pathlib import Path
import json
import requests
import stanza
import pandas as pd
```

## Query PubTator Central

First, let's retrieve some article text using the [PubTator Central API](https://www.ncbi.nlm.nih.gov/research/pubtator/api.html).

Depending on what we are interested in, we can either retrieve article abstracts or full-texts.

For the former, we provide one or more Pubmed ID's ("pmids"), and for the later, one or more Pubmed Central ID's ("pmcids").

Below, we will retrieve first the abstract, and then the full-text for a recently published article on venetoclax sensitivity in multiple myeloma:

- [Electron transport chain activity is a predictor and target for venetoclax sensitivity in multiple myeloma (Bajpai et al., 2020)](https://www.nature.com/articles/s41467-020-15051-z)


```python
# Pubmed id to query 
pmid = "32144272"
pmcid = "PMC7060223"
        
# PubTator Central API
base_url = "https://www.ncbi.nlm.nih.gov/research/pubtator-api/publications/export/biocjson"

# retrieve a single article abstract
url = f"{base_url}?pmids={pmid}"

# display api url to be queried
print(url)
```

    https://www.ncbi.nlm.nih.gov/research/pubtator-api/publications/export/biocjson?pmids=32144272



```python
# submit query and check response code to make sure it was successful
response = requests.get(url)

if response.status_code != 200:
    raise Exception(response.text)
```

Since we requested a response in the "biocjson" format, the response text will contain a block of JSON text, which we can easily convert to a Python dict using the `response.json()` function.


```python
res = response.json()
res
```




    {'_id': '32144272|None',
     'id': '32144272',
     'infons': {},
     'passages': [{'infons': {'journal': 'Nat Commun; 2020 Mar 06 ; 11 (1) 1228. doi:10.1038/s41467-020-15051-z',
        'year': '2020',
        'article-id_pmc': 'PMC7060223',
        'type': 'title',
        'authors': 'Bajpai R, Sharma A, Achreja A, Edgar CL, Wei C, Siddiqa AA, Gupta VA, Matulis SM, McBrayer SK, Mittal A, Rupji M, Barwick BG, Lonial S, Nooka AK, Boise LH, Nagrath D, Shanmugam M, ',
        'section': 'Title'},
       'offset': 0,
       'text': 'Electron transport chain activity is a predictor and target for venetoclax sensitivity in multiple myeloma.',
       'sentences': [],
       'annotations': [{'id': '1',
         'infons': {'identifier': 'MESH:D009101', 'type': 'Disease'},
         'text': 'multiple myeloma',
         'locations': [{'offset': 90, 'length': 16}]}],
       'relations': []},
      {'infons': {'type': 'abstract', 'section': 'Abstract'},
       'offset': 108,
       'text': "The BCL-2 antagonist venetoclax is highly effective in multiple myeloma (MM) patients exhibiting the 11;14 translocation, the mechanistic basis of which is unknown. In evaluating cellular energetics and metabolism of t(11;14) and non-t(11;14) MM, we determine that venetoclax-sensitive myeloma has reduced mitochondrial respiration. Consistent with this, low electron transport chain (ETC) Complex I and Complex II activities correlate with venetoclax sensitivity. Inhibition of Complex I, using IACS-010759, an orally bioavailable Complex I inhibitor in clinical trials, as well as succinate ubiquinone reductase (SQR) activity of Complex II, using thenoyltrifluoroacetone (TTFA) or introduction of SDHC R72C mutant, independently sensitize resistant MM to venetoclax. We demonstrate that ETC inhibition increases BCL-2 dependence and the 'primed' state via the ATF4-BIM/NOXA axis. Further, SQR activity correlates with venetoclax sensitivity in patient samples irrespective of t(11;14) status. Use of SQR activity in a functional-biomarker informed manner may better select for MM patients responsive to venetoclax therapy.",
       'sentences': [],
       'annotations': [{'id': '21',
         'infons': {'identifier': '596', 'type': 'Gene', 'ncbi_homologene': '527'},
         'text': 'BCL-2',
         'locations': [{'offset': 112, 'length': 5}]},
        {'id': '22',
         'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
         'text': 'venetoclax',
         'locations': [{'offset': 129, 'length': 10}]},
        {'id': '23',
         'infons': {'identifier': 'MESH:D009101', 'type': 'Disease'},
         'text': 'multiple myeloma',
         'locations': [{'offset': 163, 'length': 16}]},
        {'id': '24',
         'infons': {'identifier': 'MESH:D009101', 'type': 'Disease'},
         'text': 'MM',
         'locations': [{'offset': 181, 'length': 2}]},
        {'id': '25',
         'infons': {'identifier': '9606', 'type': 'Species'},
         'text': 'patients',
         'locations': [{'offset': 185, 'length': 8}]},
        {'id': '26',
         'infons': {'identifier': 'MESH:D009101', 'type': 'Disease'},
         'text': 'MM',
         'locations': [{'offset': 351, 'length': 2}]},
        {'id': '27',
         'infons': {'identifier': 'MESH:D009101', 'type': 'Disease'},
         'text': 'myeloma',
         'locations': [{'offset': 394, 'length': 7}]},
        {'id': '28',
         'infons': {'identifier': 'MESH:D013804', 'type': 'Chemical'},
         'text': 'thenoyltrifluoroacetone',
         'locations': [{'offset': 758, 'length': 23}]},
        {'id': '29',
         'infons': {'identifier': 'MESH:D013804', 'type': 'Chemical'},
         'text': 'TTFA',
         'locations': [{'offset': 783, 'length': 4}]},
        {'id': '30',
         'infons': {'identifier': 'p.R72C',
          'type': 'Mutation',
          'subtype': 'ProteinMutation',
          'originalIdentifier': 'p.R72C;CorrespondingGene:596',
          'hgvs': 'p.R72C',
          'gene_id': 596},
         'text': 'R72C',
         'locations': [{'offset': 813, 'length': 4}]},
        {'id': '31',
         'infons': {'identifier': 'MESH:D009101', 'type': 'Disease'},
         'text': 'MM',
         'locations': [{'offset': 860, 'length': 2}]},
        {'id': '32',
         'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
         'text': 'venetoclax',
         'locations': [{'offset': 866, 'length': 10}]},
        {'id': '33',
         'infons': {'identifier': '596', 'type': 'Gene', 'ncbi_homologene': '527'},
         'text': 'BCL-2',
         'locations': [{'offset': 923, 'length': 5}]},
        {'id': '34',
         'infons': {'identifier': '468',
          'type': 'Gene',
          'ncbi_homologene': '1266'},
         'text': 'ATF4',
         'locations': [{'offset': 971, 'length': 4}]},
        {'id': '35',
         'infons': {'identifier': '5366',
          'type': 'Gene',
          'ncbi_homologene': '88883'},
         'text': 'NOXA',
         'locations': [{'offset': 980, 'length': 4}]},
        {'id': '36',
         'infons': {'identifier': '9606', 'type': 'Species'},
         'text': 'patient',
         'locations': [{'offset': 1055, 'length': 7}]},
        {'id': '37',
         'infons': {'identifier': 'MESH:D009101', 'type': 'Disease'},
         'text': 'MM',
         'locations': [{'offset': 1188, 'length': 2}]},
        {'id': '38',
         'infons': {'identifier': '9606', 'type': 'Species'},
         'text': 'patients',
         'locations': [{'offset': 1191, 'length': 8}]},
        {'id': '39',
         'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
         'text': 'venetoclax',
         'locations': [{'offset': 1214, 'length': 10}]}],
       'relations': []}],
     'relations': [],
     'pmid': 32144272,
     'pmcid': None,
     'created': {'$date': 1592771113165},
     'accessions': ['gene@468',
      'chemical@MESH:C579720',
      'disease@MESH:D009101',
      'gene@596',
      'gene@5366',
      'mutation@p.R72C',
      'species@9606',
      'chemical@MESH:D013804'],
     'journal': 'Nat Commun',
     'year': 2020,
     'authors': ['Bajpai R',
      'Sharma A',
      'Achreja A',
      'Edgar CL',
      'Wei C',
      'Siddiqa AA',
      'Gupta VA',
      'Matulis SM',
      'McBrayer SK',
      'Mittal A',
      'Rupji M',
      'Barwick BG',
      'Lonial S',
      'Nooka AK',
      'Boise LH',
      'Nagrath D',
      'Shanmugam M']}



The response contains two main parts ("passages") relating to:

1. Article title
2. Article abstract

Each passage includes the text, some metadata, and any Pubtator annotations.

For now, let's get the abstract text so that we can infer our own annotations using Stanza.


```python
abstract = res['passages'][1]['text']

print(abstract)
```

    The BCL-2 antagonist venetoclax is highly effective in multiple myeloma (MM) patients exhibiting the 11;14 translocation, the mechanistic basis of which is unknown. In evaluating cellular energetics and metabolism of t(11;14) and non-t(11;14) MM, we determine that venetoclax-sensitive myeloma has reduced mitochondrial respiration. Consistent with this, low electron transport chain (ETC) Complex I and Complex II activities correlate with venetoclax sensitivity. Inhibition of Complex I, using IACS-010759, an orally bioavailable Complex I inhibitor in clinical trials, as well as succinate ubiquinone reductase (SQR) activity of Complex II, using thenoyltrifluoroacetone (TTFA) or introduction of SDHC R72C mutant, independently sensitize resistant MM to venetoclax. We demonstrate that ETC inhibition increases BCL-2 dependence and the 'primed' state via the ATF4-BIM/NOXA axis. Further, SQR activity correlates with venetoclax sensitivity in patient samples irrespective of t(11;14) status. Use of SQR activity in a functional-biomarker informed manner may better select for MM patients responsive to venetoclax therapy.


## Named Entity Recognition

### Process article abstract

To begin, we will download and initialize a model which uses:

- [CRAFT](https://github.com/UCDenver-ccp/CRAFT) tokenization
- [BioNLP13CG](http://2013.bionlp-st.org/) NER


```python
# download model and initialize pipeline
stanza.download('en', package='craft', processors={'ner': 'bionlp13cg'})

nlp = stanza.Pipeline('en', package='craft', processors={'ner': 'bionlp13cg'})
```

    Downloading https://raw.githubusercontent.com/stanfordnlp/stanza-resources/master/resources_1.0.0.json: 120kB [00:00, 11.0MB/s]                    
    2020-08-12 10:09:22 INFO: Downloading these customized packages for language: en (English)...
    ================================
    | Processor       | Package    |
    --------------------------------
    | tokenize        | craft      |
    | pos             | craft      |
    | lemma           | craft      |
    | depparse        | craft      |
    | ner             | bionlp13cg |
    | backward_charlm | pubmed     |
    | forward_charlm  | pubmed     |
    | pretrain        | craft      |
    ================================
    
    2020-08-12 10:09:22 INFO: File exists: /home/keith/stanza_resources/en/tokenize/craft.pt.
    2020-08-12 10:09:22 INFO: File exists: /home/keith/stanza_resources/en/pos/craft.pt.
    2020-08-12 10:09:22 INFO: File exists: /home/keith/stanza_resources/en/lemma/craft.pt.
    2020-08-12 10:09:23 INFO: File exists: /home/keith/stanza_resources/en/depparse/craft.pt.
    2020-08-12 10:09:23 INFO: File exists: /home/keith/stanza_resources/en/ner/bionlp13cg.pt.
    2020-08-12 10:09:23 INFO: File exists: /home/keith/stanza_resources/en/backward_charlm/pubmed.pt.
    2020-08-12 10:09:23 INFO: File exists: /home/keith/stanza_resources/en/forward_charlm/pubmed.pt.
    2020-08-12 10:09:23 INFO: File exists: /home/keith/stanza_resources/en/pretrain/craft.pt.
    2020-08-12 10:09:23 INFO: Finished downloading models and saved to /home/keith/stanza_resources.
    2020-08-12 10:09:23 INFO: Loading these models for language: en (English):
    ==========================
    | Processor | Package    |
    --------------------------
    | tokenize  | craft      |
    | pos       | craft      |
    | lemma     | craft      |
    | depparse  | craft      |
    | ner       | bionlp13cg |
    ==========================
    
    2020-08-12 10:09:23 INFO: Use device: gpu
    2020-08-12 10:09:23 INFO: Loading: tokenize
    2020-08-12 10:09:28 INFO: Loading: pos
    2020-08-12 10:09:29 INFO: Loading: lemma
    2020-08-12 10:09:29 INFO: Loading: depparse
    2020-08-12 10:09:29 INFO: Loading: ner
    2020-08-12 10:09:30 INFO: Done loading processors!



```python
# now we are ready to annotate some text..
doc = nlp(abstract)
```


```python
# the result it, not surprisingly, a stanza "Document" instance
print(type(doc))

# public attributes / methods
[x for x in dir(doc) if not x.startswith('_')]
```

    <class 'stanza.models.common.doc.Document'>





    ['build_ents',
     'entities',
     'ents',
     'get',
     'get_mwt_expansions',
     'iter_tokens',
     'iter_words',
     'num_tokens',
     'num_words',
     'sentences',
     'set',
     'set_mwt_expansions',
     'text',
     'to_dict']




```python
# number of entities?
len(doc.entities)

# what does a single entity result look like?
doc.entities[0]
```




    {
      "text": "BCL-2",
      "type": "GENE_OR_GENE_PRODUCT",
      "start_char": 4,
      "end_char": 9
    }




```python
# build a table containing all of the entity annotations
rows = []

for ent in doc.entities:
    rows.append([ent.text, ent.type])
    
dat = pd.DataFrame(rows, columns=['text', 'type'])

dat.sort_values(['type', 'text'])

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>text</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>MM</td>
      <td>CANCER</td>
    </tr>
    <tr>
      <th>21</th>
      <td>MM</td>
      <td>CANCER</td>
    </tr>
    <tr>
      <th>2</th>
      <td>multiple myeloma</td>
      <td>CANCER</td>
    </tr>
    <tr>
      <th>7</th>
      <td>myeloma</td>
      <td>CANCER</td>
    </tr>
    <tr>
      <th>5</th>
      <td>cellular</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>8</th>
      <td>mitochondrial</td>
      <td>CELLULAR_COMPONENT</td>
    </tr>
    <tr>
      <th>25</th>
      <td>ATF4</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>0</th>
      <td>BCL-2</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>24</th>
      <td>BCL-2</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>26</th>
      <td>BIM</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Complex I</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Complex I</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Complex II</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>17</th>
      <td>Complex II</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>23</th>
      <td>ETC</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>27</th>
      <td>NOXA</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>20</th>
      <td>SDHC</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>28</th>
      <td>SQR</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>31</th>
      <td>SQR</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>9</th>
      <td>electron transport chain</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>15</th>
      <td>succinate ubiquinone reductase</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>30</th>
      <td>patient</td>
      <td>ORGANISM</td>
    </tr>
    <tr>
      <th>4</th>
      <td>patients</td>
      <td>ORGANISM</td>
    </tr>
    <tr>
      <th>32</th>
      <td>patients</td>
      <td>ORGANISM</td>
    </tr>
    <tr>
      <th>14</th>
      <td>IACS-010759</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>16</th>
      <td>SQR</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>19</th>
      <td>TTFA</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>18</th>
      <td>thenoyltrifluoroacetone</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>1</th>
      <td>venetoclax</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>6</th>
      <td>venetoclax</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>12</th>
      <td>venetoclax</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>22</th>
      <td>venetoclax</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>29</th>
      <td>venetoclax</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>33</th>
      <td>venetoclax</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
  </tbody>
</table>
</div>



Next, it would be interesting to see how this compares to PubTatorCentral's own annotations..

Recall that, in addition the the text itself, PTC API requests also return lists of annotations -- this is what PTC was built for!


```python
annot = res['passages'][1]['annotations']
```


```python
annot[0:3]
```




    [{'id': '21',
      'infons': {'identifier': '596', 'type': 'Gene', 'ncbi_homologene': '527'},
      'text': 'BCL-2',
      'locations': [{'offset': 112, 'length': 5}]},
     {'id': '22',
      'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
      'text': 'venetoclax',
      'locations': [{'offset': 129, 'length': 10}]},
     {'id': '23',
      'infons': {'identifier': 'MESH:D009101', 'type': 'Disease'},
      'text': 'multiple myeloma',
      'locations': [{'offset': 163, 'length': 16}]}]




```python
# let's construct a table similar to the one we built for the Stanza result
ptc_rows = []

for ent in annot:
    ptc_rows.append([ent['text'], ent['infons']['type']])
    
dat_ptc = pd.DataFrame(ptc_rows, columns = ['text', 'type'])

dat_ptc.sort_values(['type', 'text'])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>text</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>8</th>
      <td>TTFA</td>
      <td>Chemical</td>
    </tr>
    <tr>
      <th>7</th>
      <td>thenoyltrifluoroacetone</td>
      <td>Chemical</td>
    </tr>
    <tr>
      <th>1</th>
      <td>venetoclax</td>
      <td>Chemical</td>
    </tr>
    <tr>
      <th>11</th>
      <td>venetoclax</td>
      <td>Chemical</td>
    </tr>
    <tr>
      <th>18</th>
      <td>venetoclax</td>
      <td>Chemical</td>
    </tr>
    <tr>
      <th>3</th>
      <td>MM</td>
      <td>Disease</td>
    </tr>
    <tr>
      <th>5</th>
      <td>MM</td>
      <td>Disease</td>
    </tr>
    <tr>
      <th>10</th>
      <td>MM</td>
      <td>Disease</td>
    </tr>
    <tr>
      <th>16</th>
      <td>MM</td>
      <td>Disease</td>
    </tr>
    <tr>
      <th>2</th>
      <td>multiple myeloma</td>
      <td>Disease</td>
    </tr>
    <tr>
      <th>6</th>
      <td>myeloma</td>
      <td>Disease</td>
    </tr>
    <tr>
      <th>13</th>
      <td>ATF4</td>
      <td>Gene</td>
    </tr>
    <tr>
      <th>0</th>
      <td>BCL-2</td>
      <td>Gene</td>
    </tr>
    <tr>
      <th>12</th>
      <td>BCL-2</td>
      <td>Gene</td>
    </tr>
    <tr>
      <th>14</th>
      <td>NOXA</td>
      <td>Gene</td>
    </tr>
    <tr>
      <th>9</th>
      <td>R72C</td>
      <td>Mutation</td>
    </tr>
    <tr>
      <th>15</th>
      <td>patient</td>
      <td>Species</td>
    </tr>
    <tr>
      <th>4</th>
      <td>patients</td>
      <td>Species</td>
    </tr>
    <tr>
      <th>17</th>
      <td>patients</td>
      <td>Species</td>
    </tr>
  </tbody>
</table>
</div>



A decent amount of overlap, but also some important differences:


- The compound "IACS-010759" only detected in Stanza
- The gene "BIM" only detected in Stanza
- SQR (enzyme complex) only detected in Stanza, but marked as a "Chemical"
- Stanza also tags "electron transport chain" as a "Gene or gene product", which is a bit of a stretch..
- PTC on the other hand detects "R72C" a mutation; AFAIK, none of the Stanza Bio models currently include mutations, so if this is your goal, this could be pretty important..

### Full-text

Next, let's try the same approach, but applied to the full-text for the same article.


```python
# Pubmed id to query 
pmcid = "PMC7060223"

# query url
url = f"{base_url}?pmcids={pmcid}"

response = requests.get(url)

if response.status_code != 200:
    raise Exception(response.text)

res = response.json()
```


```python
# In this case, instead of just two passages (title/abstract), 
# we now have ~180 passages, each one corresponding to a section
# (header, sub-header, paragraph, etc.) in the article..
len(res['passages'])
```




    184




```python
# example: intro paragraph
res['passages'][6]
```




    {'infons': {'section_type': 'INTRO',
      'type': 'paragraph',
      'section': 'Introduction'},
     'offset': 3772,
     'text': 'BH3 mimetics are a class of small molecules that block the interaction of specific proapoptotics with cognate antiapoptotics, releasing bound proapoptotic activators. Venetoclax is one such selective, potent BCL-2 antagonist. It is highly effective in BCL-2-dependent malignancies and FDA-approved for the treatment of chronic lymphocytic leukemia (CLL) and with hypomethylating agents azacitidine or decitabine (NCT02203773) or low dose cytarabine (NCT02287233) in acute myeloid leukemia (AML). Intriguingly, a small fraction (approximately 7%) of MM patients (about 40% of the 15-20% of patients exhibiting the 11;14 translocation) respond to single-agent venetoclax. Given the plethora of new myeloma therapies, there is need for precision therapy informed by biomarkers or molecular traits. Understanding the basis for single-agent efficacy of venetoclax in t(11;14) myeloma can be highly informative for identifying patients who will benefit from single-agent venetoclax therapy as well as identify targets for developing rational venetoclax containing combinations to expand use of venetoclax beyond the small cohort of patients currently sensitive to venetoclax monotherapy.',
     'sentences': [],
     'annotations': [{'id': '124',
       'infons': {'identifier': '596', 'type': 'Gene', 'ncbi_homologene': '527'},
       'text': 'BCL-2',
       'locations': [{'offset': 3980, 'length': 5}]},
      {'id': '125',
       'infons': {'identifier': '596', 'type': 'Gene', 'ncbi_homologene': '527'},
       'text': 'BCL-2',
       'locations': [{'offset': 4024, 'length': 5}]},
      {'id': '126',
       'infons': {'identifier': '9606', 'type': 'Species'},
       'text': 'patients',
       'locations': [{'offset': 4324, 'length': 8}]},
      {'id': '127',
       'infons': {'identifier': '9606', 'type': 'Species'},
       'text': 'patients',
       'locations': [{'offset': 4361, 'length': 8}]},
      {'id': '128',
       'infons': {'identifier': '9606', 'type': 'Species'},
       'text': 'patients',
       'locations': [{'offset': 4693, 'length': 8}]},
      {'id': '129',
       'infons': {'identifier': '9606', 'type': 'Species'},
       'text': 'patients',
       'locations': [{'offset': 4898, 'length': 8}]},
      {'id': '130',
       'infons': {'identifier': 'MESH:C006008', 'type': 'Chemical'},
       'text': 'BH3',
       'locations': [{'offset': 3772, 'length': 3}]},
      {'id': '131',
       'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
       'text': 'Venetoclax',
       'locations': [{'offset': 3939, 'length': 10}]},
      {'id': '132',
       'infons': {'identifier': 'MESH:D001374', 'type': 'Chemical'},
       'text': 'azacitidine',
       'locations': [{'offset': 4158, 'length': 11}]},
      {'id': '133',
       'infons': {'identifier': 'MESH:D000077209', 'type': 'Chemical'},
       'text': 'decitabine',
       'locations': [{'offset': 4173, 'length': 10}]},
      {'id': '134',
       'infons': {'identifier': 'MESH:D003561', 'type': 'Chemical'},
       'text': 'cytarabine',
       'locations': [{'offset': 4210, 'length': 10}]},
      {'id': '135',
       'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
       'text': 'venetoclax',
       'locations': [{'offset': 4620, 'length': 10}]},
      {'id': '136',
       'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
       'text': 'venetoclax',
       'locations': [{'offset': 4737, 'length': 10}]},
      {'id': '137',
       'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
       'text': 'venetoclax',
       'locations': [{'offset': 4808, 'length': 10}]},
      {'id': '138',
       'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
       'text': 'venetoclax',
       'locations': [{'offset': 4860, 'length': 10}]},
      {'id': '139',
       'infons': {'identifier': 'MESH:C579720', 'type': 'Chemical'},
       'text': 'venetoclax',
       'locations': [{'offset': 4930, 'length': 10}]},
      {'id': '140',
       'infons': {'identifier': 'MESH:D009369;-2.1889666654487105E-4',
        'type': 'Disease'},
       'text': 'malignancies',
       'locations': [{'offset': 4040, 'length': 12}]},
      {'id': '141',
       'infons': {'identifier': 'MESH:D015451;0.03791631095472116',
        'type': 'Disease'},
       'text': 'chronic lymphocytic leukemia',
       'locations': [{'offset': 4091, 'length': 28}]},
      {'id': '142',
       'infons': {'identifier': 'MESH:D015470;0.04007139993542181',
        'type': 'Disease'},
       'text': 'acute myeloid leukemia',
       'locations': [{'offset': 4238, 'length': 22}]},
      {'id': '143',
       'infons': {'identifier': 'MESH:D015470;-0.0050291200535488405',
        'type': 'Disease'},
       'text': 'AML',
       'locations': [{'offset': 4262, 'length': 3}]},
      {'id': '144',
       'infons': {'identifier': 'MESH:D009101;-0.005725198328086888',
        'type': 'Disease'},
       'text': 'myeloma',
       'locations': [{'offset': 4468, 'length': 7}]},
      {'id': '145',
       'infons': {'identifier': 'MESH:D009101;6.887764706870643E-4',
        'type': 'Disease'},
       'text': 'myeloma',
       'locations': [{'offset': 4643, 'length': 7}]}],
     'relations': []}




```python
# example: figure caption
res['passages'][10]
```




    {'infons': {'section_type': 'FIG',
      'file': '41467_2020_15051_Fig1_HTML.jpg',
      'id': 'Fig1',
      'type': 'fig_title_caption',
      'section': 'Results'},
     'offset': 6288,
     'text': 'Venetoclax-sensitive MM exhibits reduced cellular energetics in contrast to the venetoclax-resistant cells.',
     'sentences': [],
     'annotations': [],
     'relations': []}




```python
# so, suppose we want to get all of the "paragraph" text entries, we could do something like:
paragraphs = [x['text'] for x in res['passages'] if x['infons']['type'] == 'paragraph']
```


```python
len(paragraphs)
```




    53




```python
# we can then process then with Stanza, just like before..
rows = []

for i, paragraph in enumerate(paragraphs):
    doc = nlp(paragraph)
     
    for ent in doc.entities:
        # here, we add a third entry in each row to keep track of the paragraph number
        # the annotation came from
        rows.append([i, ent.text, ent.type])
```


```python
# convert to a pandas DataFrame
dat = pd.DataFrame(rows, columns=['paragraph', 'text', 'type'])

dat.shape

# show 25 randomly-selected annotations
dat.sample(25).sort_values(['type', 'text'])
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>paragraph</th>
      <th>text</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>57</th>
      <td>2</td>
      <td>acute myeloid leukemia</td>
      <td>CANCER</td>
    </tr>
    <tr>
      <th>780</th>
      <td>27</td>
      <td>B-cell lineage</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>1032</th>
      <td>34</td>
      <td>Cells</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>1016</th>
      <td>32</td>
      <td>HBL-1</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>578</th>
      <td>18</td>
      <td>MM cells</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>343</th>
      <td>12</td>
      <td>MM lines</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>297</th>
      <td>11</td>
      <td>SUDHL-6</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>739</th>
      <td>26</td>
      <td>cell</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>548</th>
      <td>17</td>
      <td>cells</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>1129</th>
      <td>38</td>
      <td>cells</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>1215</th>
      <td>41</td>
      <td>cells</td>
      <td>CELL</td>
    </tr>
    <tr>
      <th>225</th>
      <td>8</td>
      <td>inner membrane</td>
      <td>CELLULAR_COMPONENT</td>
    </tr>
    <tr>
      <th>458</th>
      <td>14</td>
      <td>ATF4</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>501</th>
      <td>15</td>
      <td>BCL-2</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>49</th>
      <td>2</td>
      <td>BCL-2</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>81</th>
      <td>3</td>
      <td>BIM</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>466</th>
      <td>14</td>
      <td>BIM</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>160</th>
      <td>6</td>
      <td>ETC</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>798</th>
      <td>27</td>
      <td>SQR</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>526</th>
      <td>16</td>
      <td>TTFA</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>540</th>
      <td>17</td>
      <td>ubiquinone oxidoreductase</td>
      <td>GENE_OR_GENE_PRODUCT</td>
    </tr>
    <tr>
      <th>1066</th>
      <td>35</td>
      <td>CHAPS</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>1121</th>
      <td>38</td>
      <td>DCPIP</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>737</th>
      <td>26</td>
      <td>glutamine</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
    <tr>
      <th>625</th>
      <td>21</td>
      <td>venetoclax</td>
      <td>SIMPLE_CHEMICAL</td>
    </tr>
  </tbody>
</table>
</div>



# Final thoughts

In the above, we really only just scratched the surface of what you can do with either PubTator Central or Stanza.

Hopefully it at least provided a sense of some of the kinds of analyses one can use these tools for, as well as how easy it can be to annotate scientific text.

A few examples of things you could do from here:

- Construct annotation co-occurence matrices and use them to detect unexpected `<gene, drug>`, `<gene, disease>`, etc. pairs across a range of documents.
- Use a [word embedding approach](https://towardsdatascience.com/document-embedding-techniques-fed3e7a6a25d) to create a vector representation of a collection of articles, followed by [vector similarity](https://medium.com/@adriensieg/text-similarities-da019229c894) to assess the similarity of the texts.
- Using a co-occurence matrix of Genes, Drugs, and SNPs (annotated in PubTator Central), build a weighted heterogenous network with each annotation represented as a node and edge weights assigned as a function of the co-occurence counts. This could then be followed by [community detection](https://www.analyticsvidhya.com/blog/2020/04/community-detection-graphs-networks/) to detect groups of interacting drugs, genes, and mutations.


```python

```
