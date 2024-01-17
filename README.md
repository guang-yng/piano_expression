# Sketching the Expression: Flexible Rendering of Expressive Piano Performance with Self-Supervised Learning
* Paper: https://arxiv.org/abs/2208.14867
* Demo: https://free-pig-6c6.notion.site/DEMO-c20a1fea7a0844468a05b971c3b9ef3c


## Installation

Clone this repository, and install the required packages: 

```
git clone https://github.com/rsy1026/sketching_piano_expression.git
pip install -r requirements.txt
```

Then run this command to make `sketching_piano_expression` a python package for importing inner functions easily:

```
pip install -e .
```

## Parsing features for training
We should align three files to parse the features: score MusicXML, score MIDI and human performance MIDI. Raw data samples can be found in `./scripts/data/data_samples/raw_samples`.

Please make sure that MusicXML data has no error: 
* Please check if any notes are *hidden* by some annotations (such as trills, glissandos, etc.) in the MusicXML score (You may check with the *MuseScore* software).
* Trills should not be abbreviated as "tr." signs but all notes should appear within each trill.
* Make sure that any complex techniques are not abbreviated as annotations but every single note should be written in the MusicXML score.
* Functions to extract the features may be updated in the future for imperfectly aligned MusicXML-MIDI files. These functions may flexibly skip the unaligned notes.


### Parsing a single set of file

```
python3 extract_features.py --xml [filename1].musicxml --score [filename2].mid --perform [filename3].mid --measures [num1] [num2]
```

### Saving batches

Enter to 'scripts' directory, and run the following command:

```
python3 ./data/make_batches.py --input_dir ./data/data_samples/features
```

You can make h5 files using the following function in `make_batches.py`:

```
create_h5_datasets(dataset='train', savepath='./data/data_samples')
```

### Data Representation

In dataset (train.h5)
```
x: note x 136 matrix containing score features.
y: note x 4 matrix containing performance features, including velocity, duration, ioi1 and ioi2. Articulation could be calculated by dur-ioi2.
m: note x chord matrix.
c: note x 59 matrix. 19-23 dim is used to construct `x`` during training.
```

Model input
```
x: note x (136-dim vector containing score features + 4-dim vector from c in data).
y: note x 3 matrix containing velocity, articulation and ioi (ioi1 in dataset y). Interpolated to the range of [-1, 1].
y2: chord x 3 matrix containing chord y.
m: note x chord matrix.
clab: chord x 3 matrix \hat{y} predicted by 4-degree polynomial regression on (range(chord), y2)
```

## Training 

Simply run the following command:

```
python3 train.py
```

You may change any settings within the code.
