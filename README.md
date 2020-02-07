### Data pre-processing

To prepare the data for training, start with the following command:

`bash prepare-iwslt15.sh`

 This will download the data from [IWSLT'15 English-Vietnamese dataset](https://nlp.stanford.edu/projects/nmt/), clean and tokenize it using [Moses scripts](https://github.com/moses-smt/mosesdecoder/), and finally apply BPE ([Sennrich et al.](https://github.com/rsennrich/subword-nmt)) on the data.

This script sets the number of BPE tokens to 4000 and applies BPE dropout with `p = 0.1` by default. To disable BPE dropout, remove the optional argument `--dropout 0.1` in the following line:

`python $BPEROOT/apply_bpe.py -c $BPE_CODE --dropout 0.1 < $tmp/train.$L > $tmp/bpe.train.$L`


To binarize the data, run:

```
TEXT=iwslt15.tokenized.en-vi

fairseq-preprocess   --source-lang en --target-lang vi --trainpref $TEXT/train --validpref $TEXT/valid  --testpref $TEXT/test --destdir data-bin/iwslt15_en_vi_bpe4k --workers 20
```

### Training

To start the training, run:

```
CUDA_VISIBLE_DEVICES=0 fairseq-train data-bin/iwslt15_en_vi_bpe4k/ --lr 0.0001 --optimizer adam --clip-norm 0.0 --dropout 0.2 --max-tokens 4000 --batch-size 32 --arch transformer --update-freq 16 --save-dir checkpoints --no-epoch-checkpoints
```

All model checkpoints are saved in `./checkpoints`. Since `--no-epoch-checkpoints` is set, only the last checkpoint and the best checkpoint will be saved.

### Translation

To generate English to Vietnamese translations, run:

```
fairseq-generate data-bin/iwslt15_en_vi_bpe4k/  --source-lang en --target-lang vi --path checkpoints/checkpoint_best.pt --batch-size 8 --beam 4 --remove-bpe`
```

Translations are generated from the best model checkpoint, using beam search with default beam width 4. `remove-bpe` is set to remove BPE delimiters for easier reading.

## Progress & report

Experimental results can be found [here](https://docs.google.com/spreadsheets/d/1eJP9KRe7F8wSMduCsVd17VZzZ3HgH-CDl6nkSpQkPeE/edit#gid=0).

Thesis: https://www.overleaf.com/read/xgftpjdvccvb
