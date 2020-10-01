# Bertserini: Baseline on CMRC QA (in Chinese)

1. Clone the repo with ```git clone https://github.com/rsvp-ai/bertserini.git```
2. ```pip install -r requirements.txt```

## Download PreBuilt Wikipedia Index

We have indexed the 2018 Wikipedia Chinese dump. You can download the prepared index here:
```
wget https://www.dropbox.com/s/6zn16mombt0wirs/lucene-index.zhwiki-20181201-paragraphs.tar.gz?dl=0
```
It contains the indexed 20181201 Chinese Wikipedia dump with Anserini.

After unzipping these files, put them under the root path of this repo, and then you are ready to go.
Take the following folder structure as an example:
```
bertserini
+--- indexes
|    +--- lucene-index.zhwiki-20181201-paragraphs
|    |    +--- ...
+--- other files under this repo
```

## Download the pre-trained models

We have uploaded the finetuned checkpoints to the huggingface models: \
[bertserini-bert-base-cmrc](https://huggingface.co/rsvp-ai/bertserini-bert-base-cmrc)


To run our finetuned model, just set ```--model_name_or_path rsvp-ai/<MODEL_NAME>```.  
For example: ```--model_name_or_path rsvp-ai/bertserini-bert-base-cmrc```.

# Inferencing and evaluating

## Prepare the datasets:

```
cd data
wget https://worksheets.codalab.org/rest/bundles/0xb70e5e281fcd437d9aa8f1c4da107ae4/contents/blob/
mv index.html cmrc2018_dev.json
wget https://worksheets.codalab.org/rest/bundles/0x72252619f67b4346a85e122049c3eabd/contents/blob/
mv index.html cmrc2018_dev_squad.json
```

## Inferencing CMRC under the open-domain setting
For `rsvp-ai/bertserini-bert-base-cmrc`
```
python -m bertserini.experiments.inference --dataset_path data/cmrc2018_dev_squad.json \
                                           --index_path indexes/lucene-index.zhwiki-20181201-paragraphs \
                                           --model_name_or_path rsvp-ai/bertserini-bert-base-cmrc \
                                           --output prediction/cmrc2018_pred.json \
                                           --topk 10 \
                                           --language zh

```

## Evaluation

```
mkdir temp
python -m bertserini.experiments.evaluate --eval_data data/cmrc2018_dev.json \
                                          --search_file prediction/cmrc2018_pred.json \
                                          --output_path temp \
                                          --dataset cmrc
                                          
```

Expected results:

```
## rsvp-ai/bertserini-bert-base-cmrc, this is bert-base-chinese finetuned on the chinese reading comprehension dataset(CMRC)
(0.5, {'f1_score': 67.77199045328142, 'exact_match': 50.916433675054364, 'total_count': 3219, 'skip_count': 0})
```