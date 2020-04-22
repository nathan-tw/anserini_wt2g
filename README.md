# anserini_wt2g
Anserini is a super powerful toolkit for IR, but it lacks some instructions. Here I gonna go through some of important commands and their args.

## how to index
* index with stemming

```
nohup sh target/appassembler/bin/IndexCollection -collection TrecwebCollection -input {path/to/wt2g/*.txt} -index wt2g.stemmed.index -generator DefaultLuceneDocumentGenerator -threads 16 \
 -storePositions -storeDocvectors -storeRaw >& log.wt2g.stemmed &
```
* index without stemming
```
nohup sh target/appassembler/bin/IndexCollection -collection TrecwebCollection -input {path/to/wt2g/*.txt} -index wt2g.unstemmed.index -stemmer none -generator DefaultLuceneDocumentGenerator -threads 16 \
-storePositions -storeDocvectors -storeRaw >& log.wt2g.unstemmed &
```
## how to search
* search command--BM25
```
target/appassembler/bin/SearchCollection -index wt2g.stemmed.index \
 -topicreader Trec -topics {path/to/query} \
 -bm25 -bm25.k1 2 -bm25.b 0.75 -output VSM_retrieve_stemmed.txt &
```
```
target/appassembler/bin/SearchCollection -index wt2g.unstemmed.index \
 -topicreader Trec -topics {path/to/query} \
 -bm25 -bm25.k1 2 -bm25.b 0.75 -output VSM_retrieve_unstemmed.txt &
```

* search command--Dirichlet smoothing
```
target/appassembler/bin/SearchCollection -index wt2g.unstemmed.index \
 -topicreader Trec -topics {path/to/query} \
 -qld -output qld_retrieve_unstemmed.txt &
```
```
target/appassembler/bin/SearchCollection -index wt2g.stemmed.index \
 -topicreader Trec -topics {path/to/query} \
 -qld -output qld_retrieve_stemmed.txt &
```

* search command--Jelinek-Mercer smoothing
```
target/appassembler/bin/SearchCollection -index wt2g.unstemmed.index \
 -topicreader Trec -topics {path/to/query} \
 -qljm -qljm.lambda 0.2 -output qljm_retrieve_unstemmed.txt &
```
```
target/appassembler/bin/SearchCollection -index wt2g.stemmed.index \
 -topicreader Trec -topics {path/to/query} \
 -qljm -qljm.lambda 0.2 -output qljm_retrieve_stemmed.txt &
```
## how to evaluate

* trec_eval
```
eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt VSM_retrieve_unstemmed.txt > bm25_unstemmed.txt
eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt VSM_retrieve_stemmed.txt > bm25_stemmed.txt
eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt qld_retrieve_unstemmed.txt > qld_unstemmed.txt
eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt qld_retrieve_stemmed.txt > qld_stemmed.txt
eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt qljm_retrieve_unstemmed.txt > qljm_unstemmed.txt
eval/trec_eval.9.0.4/trec_eval  src/main/resources/topics-and-qrels/qrels.adhoc.401-450.txt qljm_retrieve_stemmed.txt > qljm_stemmed.txt
```
