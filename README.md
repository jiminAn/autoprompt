# AutoPrompt


## Setup/Installation

### 1. Create conda environment and install requirements
```
conda create -n autoprompt -y python=3.7 && conda activate autoprompt
pip install -r requirements.txt
```

### 2. Download stuff
Install spacy model
```
python -m spacy download en
```

### 3. Download data
Download the data here: https://drive.google.com/drive/folders/1vVhgnSXmbuJb6GLPn_FErY1xDTh1xyv-?usp=sharing

## How to Generate Prompts

### Quick Overview of Templates
A prompt is constructed by mapping things like the original input and trigger tokens to a template that looks something like

`[CLS] {sub_label} [T] [T] [T] [P]. [SEP]`

The example above is a template for generating fact retrieval prompts with 3 trigger tokens where `{sub_label}` is a placeholder for the subject in any (subject, relation, object) triplet in fact retrieval. `[P]` denotes the placement of a special `[MASK]` token that will be used to "fill-in-the-blank" by the language model. Each trigger token in the set of trigger tokens that are shared across all prompts is denoted by `[T]`.

Depending on the language model (i.e. BERT or RoBERTa) you choose to generate prompts, the special tokens will be different. For BERT, stick `[CLS]` and `[SEP]` to each end of the template. For RoBERTa, use `<s>` and `</s>` instead.

### Fact Retrieval
```
python -m lmat.create_trigger \
    --train $path/train.jsonl \
    --dev $path/dev.jsonl \
    --template '<s> {sub_label} [T] [T] [T] [P] . </s>' \
    --num_cand 10 \
    --accumulation-steps 1 \
    --model-name roberta-base \
    --bsz 12 \
    --eval-size 12 \
    --iters 10 \
    --label-field 'obj_label' \
    --tokenize-labels \
    --filter \
    --print-lama
```

## Evaluation

### 1. Setup LAMA
Go to our fork of the LAMA repo https://github.com/taylorshin/LAMA and follow the directions to set up the LAMA repo outside of this LMAT repo.
It is recommended to create a separate conda environment for LAMA due to different dependencies and requirements.

Copy the LMAT data folder into the `data` directory of LAMA or set `data_path_pre` in `scripts/run_experiments.py` to a custom data location.

### 2. Evaluate prompts
First, update the `data/relations.jsonl` file with your own prompts whether they are manual prompts or automatically generated prompts.
Then, run the evaluation code.
```
python scripts/run_experiments.py
```

### 3. Settings
To change evaluation settings, go to `scripts/run_experiments.py` and update the PARAMETERS accordingly.
For example, set `use_context` to True for conditional prompt evaluation.

### 4. Miscellaneous
Set PYTHONPATH if the following error occurs: `ModuleNotFoundError: No module named 'lama', pip`
```
export PYTHONPATH="${PYTHONPATH}:/path/to/your/module/"
```

## Citation
```
@inproceedings{autoprompt:emnlp20,
  author = {Taylor Shin and Yasaman Razeghi and Robert L. Logan IV and Eric Wallace and Sameer Singh},
  title = { {AutoPrompt}: Automatic Prompt Construction for Masked Language Models },
  booktitle = {Empirical Methods in Natural Language Processing (EMNLP)},
  year = {2020}
}
```
