# Sumerian-ENglish-Machine-Translation
---
##### The Project is completed by implementing Machine Translation in two ways :
1. NMT Through Transformers
2. NMT through Transfer Learning

## 1. For Transfer Learning

### Installation

Clone the repository. (We are assuming you have python version 3.6.x and pip is installed on your linux system)
(Optional)If not, please use the below command, this will create a new environment using conda.

```
conda create -n env python=3.6
conda activate env
```
All dependencies can be installed via:
```
pip3 install -r requirements.txt
```
NOTE: If you have MemoryError in the install try to use:
```
pip3 install -r requirements.txt --no-cache-dir
```
Note that Project currently support Tensorflow = 1.15. We tested it on Tensorflow 1.15.0
By this point, your system should be ready with all dependencies. Please use below command to check Tensorflow verion.
```
python -c "import tensorflow; print(tensorflow.__version__)"
```
Output should be your Tensorflow version = 1.15.0
If you still face any issues while installing dependencies for the project, feel free to raise issue.

### Preprocessing :

#### 1. Tokenization
 
```
perl ./data/tokenizer.perl -l en < ./data/Sumerian_Data/english.txt > ./data/Sumerian_Data/en-su.en.all
perl ./data/tokenizer.perl -l en < ./data/Sumerian_Data/english.txt > ./data/Sumerian_Data/en-su.en.all
```
The Tokenized data is used for training procedure after applying Byte-pair Encoding using Subword-nmt. 
```
subword-nmt learn-bpe -s 2000 < ./data/Sumerian_Data/en-su.en.all > ./data/Sumerian_Data/en-su.en.codes
subword-nmt apply-bpe -c ./data/Sumerian_Data/en-su.en.codes < ./data/Sumerian_Data/en-su.en.all > ./data/Sumerian_Data/en-su.en.bped
subword-nmt learn-bpe -s 2000 < ./data/Sumerian_Data/sumerian.txt > ./data/Sumerian_Data/en-su.su.codes
subword-nmt apply-bpe -c ./data/Sumerian_Data/en-su.su.codes < ./data/Sumerian_Data/sumerian.txt > ./data/Sumerian_Data/en-su.su.bped
```

#### 2. Moving to DataSection and Division
```
cd data/Sumerian_Data/
python docen.py
```
### Training. 
```
CUDA_VISIBLE_DEVICES=0 python train.py --source_dataset data/Sumerian_Data/en-su.su.train --target_dataset data/Sumerian_Data/en-su.en.train --dictionaries data/french/train.fr-en.fr.json data/french/train.fr-en.en.json --save_freq 20000 --model model.su-en --model_type transformer --embedding_size 128 --state_size 128 --tie_decoder_embeddings --loss_function per-token-cross-entropy --label_smoothing 0.1 --exponential_smoothing 0.0001 --optimizer adam --adam_beta1 0.9 --adam_beta2 0.98 --adam_epsilon 1e-09 --learning_schedule transformer --maxlen 200 --batch_size 32 --token_batch_size 2048 --valid_source_dataset data/Sumerian_Data/en-su.su.valid --valid_target_dataset data/Sumerian_Data/en-su.en.valid --valid_freq 5000 --valid_batch_size 32 --valid_token_batch_size 2048 --reload ./model.hi-eng-80000
```

### Translatation: 

```
python translate.py -b 32 -v -k 12 -i ./data/Sumerian_Data/en-su.su.test -o ./data/ensu-out -m model.su-en-120000

cat /data/Sumerian_Data/en-su.su.test | sed -E 's/(@@ )|(@@ ?$)//g' > /data/Sumerian_Data/en-su.su.test.bpe

cat  ./data/ensu-out | sed -E 's/(@@ )|(@@ ?$)//g' > data/su-en.out
```

