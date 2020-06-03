# PBSMT for Sumerian-English-Machine-Translation
---
The PBSMT implementation supports:
- Unsupervised phrase-table generation scripts
- Automated Moses training

## Dependencies

* Python 3
* [NumPy](http://www.numpy.org/)
* [PyTorch](http://pytorch.org/) (currently tested on version 0.5)
* [Moses](http://www.statmt.org/moses/) (clean and tokenize text / train PBSMT model)
* [fastBPE](https://github.com/glample/fastBPE) (generate and apply BPE codes)
* [fastText](https://github.com/facebookresearch/fastText) (generate embeddings)
* [MUSE](https://github.com/facebookresearch/MUSE) (generate cross-lingual embeddings)

Once you have a working version of Moses, edit the `MOSES_PATH` variable inside the `PBSMT/run.sh` script to indicate the location of Moses directory. Then, simply run:
```
./run.sh
```

The script will successively:

- Install tools
    + Check Moses files
    + Download MUSE and download evaluation files
- Run MUSE to generate cross-lingual embeddings
- Generate an unsupervised phrase-table using MUSE alignments
- Run Moses
    + Create Moses configuration file
    + Run Moses on test sentences
    + Detruecase translations