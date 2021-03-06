# Soloist

**(This document was used internally during the system development.
A lot of things like paths and configurations are hardcoded based on our server configurations.
Fair amount of hacking and modifications are expected if you want to train new models. 
We apologize for not being able to provide fully tested training recipe.)** 

Commands to download the `gp2_en` model mentioned below (it is identical to the `gpt2` model released by [transformers](https://huggingface.co/transformers/pretrained_models.html)):
```bash
mkdir gpt2_en
wget -P gpt2_en/ https://storage.googleapis.com/dstc9_submission/gpt2_en/config.json
wget -P gpt2_en/ https://storage.googleapis.com/dstc9_submission/gpt2_en/merges.txt
wget -P gpt2_en/ https://storage.googleapis.com/dstc9_submission/gpt2_en/pytorch_model.bin
wget -P gpt2_en/ https://storage.googleapis.com/dstc9_submission/gpt2_en/vocab.json

``` 

## Quick Start

Train and evaluate a baseline SOLOIST model.

#### Edit configuration file
Edit [`experiment_management/config/example.json`](experiment_management/config/example.json):
* Change `train|dev|test` to the absolute path of `{train|val|test}_data_for_soloist.json`
* Download English pre-trained gpt2 from [here](http://10.186.1.122:8080/~luban/gpt2_en/). Change `pretrained_gpt` to the downloaded gpt2 model directory.
* Make sure your GPU memory is larger than 16GB.
* Change `python_path` to your dstc9 project directory. 

#### Train a model
```bash
python experiment_management/ems.py experiment_management/config/example.json train
```
Model and training log are saved under `experiment_management/model/example`.

There is no early stopping. Terminate the training manually when validation loss is not decreasing.


#### Test your model
```bash
cd methods/baseline
PYTHONPATH=<dstc9_project_dir> python test_Soloist > test.log.txt
```

Test script uses ConvLab2 test module to create an automatic user to talk to the agent. Generated dialogs and scores are saved in `test.log.txt`.


#### Distributed training on Luban

Change `node_num` to the number of nodes (machines) you want to use, then start training as the same as above. 

(this requires the installation of Luban_offline task. will add instructions for this if necessary.) 

## Try a new method

Try out your new ideas to improve the SOLOIST agent. 
You only need to write the Soloist algorithm and the rest work are handled by experiment management system ([`experiment_management`](experiment_management)).
(This part requires knowledge of the implementation of SOLOIST baseline: [`methods/baselin`](methods/baseline))

* Copy `methods/baseline` to `methods/improved_soloist`. (hard copying the baseline is to avoid interference of the two implementations)
* Correct all import paths in `methods/improved_soloist`. 
* Copy `experiment_management/config/example.json` to `experiment_management/config/improved_soloist.json`. In the json, change `method` to `improved_soloist`. 
* Implement your ides in `methods/improved_soloist`.
* Train model: 
```bash
python experiment_management/ems.py experiment_management/config/improved_soloist.json train
```
(Model and training logs are saved under: `experiment_management/model/improved_soloist`)
* Test model:
 ```bash
cd methods/improved_soloist
PYTHONPATH=<dstc9_project_dir> python test_Soloist > test.log.txt
```

## Cite

Please also cite this paper if you find the code useful:
```
@article{peng2020soloist,
  title={Soloist: Few-shot task-oriented dialog with a single pre-trained auto-regressive model},
  author={Peng, Baolin and Li, Chunyuan and Li, Jinchao and Shayandeh, Shahin and Liden, Lars and Gao, Jianfeng},
  journal={arXiv preprint arXiv:2005.05298},
  year={2020}
}
```

