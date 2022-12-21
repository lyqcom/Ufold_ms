# Content

# UFold Overview

For many RNA molecules, secondary structure is critical to the correct function of the RNA. Predicting RNA secondary structure from nucleotide sequences is a long-standing problem in genomics, but prediction performance has reached a stable level over time. Traditional RNA secondary structure prediction algorithms are mainly based on thermodynamic models by free energy minimization, which impose strong a priori assumptions and run slowly.UFold, a deep learning-based approach for RNA secondary structure prediction, is trained directly on annotation data and base pairing rules.UFold proposes a new class image representation of RNA sequences , which can be efficiently processed by fully convolutional networks (FCNs).

## Model Architecture

The input to the model is generated by taking the outer product of all combinations of the four basic channels of One-Hot Encoding, which yields 16 channels. The additional channels representing the pairing probabilities are then concatenated with the 16-channel sequence representation and used together as inputs to the model.The UFold model is a variant of U-Net that takes the 17-channel tensor as input and transforms the data by successive convolution and maximum pooling operations.

## Dataset

Several benchmark datasets were used:

- RNAStralign, containing 30,451 unique sequences from 8 RNA families;

- ArchiveII, containing 3975 sequences from 10 RNA families, is the most widely used benchmark dataset for RNA structure prediction performance;

- bpRNA-1m, containing 102,318 sequences from 2588 families, is one of the most comprehensive RNA structure datasets available;

- bpRNA new, derived from Rfam 14.2, contains sequences from 1500 new RNA families.

To facilitate the use of the dataset, we processed the data files in bpseq format into pickle files. the data files used in the UFold model can be downloaded in [drive](https://drive.google.com/drive/folders/1Sq7MVgFOshGPlumRE_hpNXadvhJKaryi), and the files need to be placed in the data folder when used.

## Requirements

- Hardware（Ascend/GPU）
    - Prepare hardware environment with Ascend or GPU processor.
- Framework
    - [MindSpore](https://www.mindspore.cn/install/en)
- For more information, please check the resources below：
    - [MindSpore Tutorials](https://www.mindspore.cn/tutorials/en/master/index.html)
    - [MindSpore Python API](https://www.mindspore.cn/docs/en/master/index.html)
- third-party libraries

```bash
pip install -r requirements.txt
```

## Quick Start

1. Download the pickle file and place it in the data folder.

2. Modify first_epoch in `config.json` to modify the number of training rounds.

3. Execute the training script.
  After the dataset is prepared, start the training as follows.

```text
python train.py --train_files dataset_A dataset_B
  --train_files: One or more datasets can be selected from (['ArchiveII','TS0','bpnew','TS1','TS2','TS3']) for training.
  --device_target： Can be selected from (['GPU', 'Ascend']).
  --device_id: Can be selected according to the environment.
```

4. Execute the inference script.
  After training, the following steps are followed to initiate inference.

```text
python ufold_test.py --test_files TS2
  --test_files: One or more datasets can be selected for inference from (['ArchiveII','TS0','bpnew','TS1','TS2','TS3']).
  --ckpt_file: Select the ckpt file to load.
  --device_target： Can be selected from (['GPU', 'Ascend']).
  --device_id: Can be selected according to the environment.
```

## Script Description

### Scripts and Sample Code

```shell
.
└─UFold
  ├─README.md                             # README
  ├─ckpt_models                           # Store the weight files generated by training with mindspore
  ├─data                                  # The pickle file obtained by pre-processing the dataset
  ├─src
    ├─config.json                         # Parameter Setting
    ├─config.py                           # Loading Settings
    ├─data_generator.py                   # Custom Data Set Classes
    ├─Network.py                          # UFold Network Definition
    ├─utils.py                            # Fragmented functions
    └─postprocess.py                      # Post-processing for optimization
  ├─eval.py                               # Evaluation Scripts
  └─train.py                              # Training Scripts
```

### Script Parameter

```bash
"BATCH_SIZE":1,
"epoch_first":100
```

## Training

- Set the configuration items in `config.json`, including BATCH_SIZE and training epoch.

### Training Process

- Run `train.py` to start the UFold training

```shell
    python train.py --train_files dataset_A
        --train_files: One or more datasets can be selected from (['ArchiveII','TS0','bpnew','TS1','TS2','TS3']) for training.
        --device_target： Can be selected from (['GPU', 'Ascend']).
        --device_id: Can be selected according to the environment.
```

- The results of the training printout are as follows.

```log
# grep "loss is " train.log
epoch:1 epoch: 1, loss: 1.4842823
epcoh:2 epoch: 2, loss: 1.0897788
```

## Evaluation

### Evaluation Process

- Run `eval.py` for evaluation.

Note: You can use the trained ckpt for evaluation, or use the trained provided [files](https://drive.google.com/drive/folders/1XmeoRaFS3iXa9kZwte99e7usB7mPLV4z?usp=sharing) for evaluation. (ufold_train_pdbfinetune.ckpt is used for TS1, TS2, TS3; ufold_train.ckpt is used for bpnew, ArchiveII and TS0; ufold_train_alldata.ckpt can be used for all the test data.)

```bash
# 推理
python ufold_test.py --test_files TS2
  --test_files: One or more datasets can be selected for inference from (['ArchiveII','TS0','bpnew','TS1','TS2','TS3']).
  --ckpt_file: Select the ckpt file to load.
  --device_target： Can be selected from (['GPU', 'Ascend']).
  --device_id: Can be selected according to the environment.
```

### Evaluation Result

The accuracy of the test data set is as follows.

```log
Average testing precision with pure post-processing: 0.781516432132
```
