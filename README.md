# South-American-Accent-Recognition

This project presents a task of *accent recognition* and *gender detection* performed on audio files of Latin American Spanish accents. The analyzed data comes from opensource speech corpora of Argentinian, Chilean, Colombian, Peruvian and Venezuelan speakers, made publicly available at `openslr.org` (cf. Guevara et al. 2020). These constitute the first high-quality free **multispeaker** dataset exhisting for these languages. 

The  files are recorded as 48 kHz single-channel audios and are divided per accent and gender of the speaker. The total number of speakers considered is 169 per 5 accents, divided in 89 women and 80 men (however it had to be reduced for computational reasons). The content of the recordings are read sentences presented on-screen. Each speaker got assigned 150 phrases. 

## Target groups

* Argentinian female speakers (3531 files)
* Argentinian male speakers (1818 files)
* Colombian female speakers (2369 files)
* Colombian male speakers (2534 files)
* Peruvian female speakers (2529 files)
* Peruvian male speakers (2918 files)
* Venezuelan female speakers (1161 files)
* Venezuelan male speakers (1754 files)
* Chilean female speakers (1738)
* Chilean male speakers (2636 files)


## Pre-processing

Different partitions for training, test and validation set (70%, 15% & 15%) containing shuffled data from each target group were created and stored in csv files. 5 experiments were performed in order to check whether the performance would have improved in case of equally distributed recordings per group. 

```
import random 

def files_per_t_red(final_list,desired_number):
  for audiofile in files_list:
    random.shuffle(audiofile)                    
    final_list.append(audiofile[:desired_number])
  return final_list
  ```
  

1.   1.000 audio files in total (selecting 100 speakers per target accent)
2.   2.000 audio files in total (selecting 200 speakers per target accent)
3.   4.000 audio files in total (selecting 400 speakers per target accent)
4.   10% of 22988 audiofiles (without equal number of speakers per target)
5.   20% of 22988 audiofiles (without equal number of speakers per target)


## Dataloader 

The dataloader takes the audio files according to the information provided in the csv files, where they are stored mixed and divided per training, validation and test set.

The max length to be considered is 64000 samples or 4s and the windows length is 0.02. When loading the audio files, the sampling rate is 1600 and the length of the original recording is limited to 4s. The dataloader returns the Melspectrogram on 40 Mel filterbanks and the labels (target name). Considered experiments and data amounts:

1. total audio files= 1000 (750 training, 150 test, 150 valid. sets)
2. total audio files= 2000 (1400 training, 300 test, 300 valid. sets)
3. total audio files= 4000 (2800 training, 600 test, 600 valid. sets)
4. total audiofiles= 22988
  4.1.(1608 training -10% initial training set- 3,448 valid, 3,454 test)
  4.2.(32,172 training, 3,448 valid, 3,454 test) 20% of the total 



## Training 

The different partitions were used to train a small model with convoluted neural networks.
```
CNNNet(
  (conv1): Conv2d(1, 64, kernel_size=(8, 20), stride=(1, 1))
  (maxpool1): MaxPool2d(kernel_size=(2, 2), stride=(2, 2), padding=0, dilation=1, ceil_mode=False)
  (conv2): Conv2d(64, 64, kernel_size=(4, 10), stride=(1, 1))
  (maxpool2): MaxPool2d(kernel_size=(1, 1), stride=(1, 1), padding=0, dilation=1, ceil_mode=False)
  (conv2_drop): Dropout2d(p=0.5, inplace=False)
  (fc1): Linear(in_features=151424, out_features=128, bias=True)
  (fc2): Linear(in_features=128, out_features=10, bias=True)
  (drop): Dropout(p=0.5, inplace=False)
)

```

 
## Evaluation 

Experiment 5, with the highest amount of training data (not equally distributed per speakers' group) obtained the highest accuracy values (61% on train and valid. set) 
Overall the best identified target groups were Argentinian Female Speakers and Chilean Male Speakers.
 
 
 
cf. Guevara-Rukoz, A., Demirsahin, I., He, F. et al. (2020). Crowdsourcing Latin American Spanish for Low-Resource Text-to-Speech.*
