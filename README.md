# Split audio according to information in srt-file.

To be able to train the speech-recognition engine `<a href='https://github.com/mozilla/DeepSpeech'>` DeepSpeech `</a>`, audio-files should not be longer than 10s.
Therefore, this repo offers the possibility to easily split audio files based on the subtitle-info in srt-files and prepare corresponding transcript files.

**Table of Contents**

- [Split audio according to information in srt-file.](#split-audio-according-to-information-in-srt-file)
  - [Quickstart](#quickstart)
    - [Easy guide for requirements](#easy-guide-for-requirements)
  - [Prerequisites](#prerequisites)
  - [Walk-through](#walk-through)
    - [Example Files](#example-files)
    - [Modules](#modules)
  - [Check characters](#check-characters)
  - [About this project:](#about-this-project)

## Quickstart

### Easy guide for requirements

* Create new conda environment with python version 3.6
  * `conda create -n "srt" python=3.6`
* Install required libraries manually or using `requirements.txt`
  * Manually: `pip install librosa soundfile pydub moviepy pandas unidecode num2words nltk`
  * Using pip list: `pip install -r requirements.txt`

### Preparing the data

* Create two directory: `audio`, `srt_files`
* Copy audio/movie (`wav,wmv,mp4`) files to  `audio` directory
* Copy caption (`srt`) files to `srt_files` directory

### Run the code

* Run `python SRT_to_CSV_and_audio_split.py`


## Prerequisites

* [Python 3.6](https://www.python.org/)
* [librosa](https://librosa.github.io/librosa/)
* [SoundFile](https://pypi.org/project/SoundFile/)
* [pydub](https://pypi.org/project/pydub/)
* [moviepy](https://zulko.github.io/moviepy/)
* pandas
* unidecode
* num2words
* nltk
  * install `punkt` from NLTK Downloader

```bash
>>> import nltk
>>> nltk.download('punkt')
```

## Walk-through

`<b>`This section will explain what the modules and script do in order to provide a deeper understanding of the individual steps and facilitate modification `</b>`

FYI: the script "convert_srt_to_csv.py" is meant to be used on srt files with encoding "cp1252" (a.k.a Windows 1252). The reason for this is that in order to keep characters such as "ä", the files have to be encoded to "utf8".

If your files are already in "utf8", then deactive the module "change_encoding".

> [!NOTE]
>
> In this fork, `change_encoding()` is disabled by default.

`<b>`First:`</b>` Create a folder called "srt_files" where you store your srt_files and a folder "audio" where you store your audio-files (wmv or mp4 or wav).

### Example Files

<p> Check the folder Example Files to see how the information is extracted from an srt-file to a csv-file.</p>

### Modules

<p><b>- change_encoding: </b>The encoding of srt-files is changed from cp1252 to utf-8.</p>
<p><b>- convert_srt_to_csv: </b>Start time, end-time and subtitle are extracted from the srt-files and stored in a csv. In preparation for audio-splitting, a column id is generated from the filename with the addition of a unique number.</p>
<p><b>- wmv_to_wav & mp4_to_wav: </b>Extraction of audio from wmv or mp4 files.</p>
<p><b>- pre_process_audio: </b>Audio is processed to meet DeepSpeechs requirements of sample-rate 16kHz and bit-rate 16.</p>
<p><b>- split_files: </b>The audio files are splitted according to the start- and end-times in the csv files. The splitted audio-files are named after the id given in the transcripts.</p>
<p><b>- create_DS_csv: </b>This module creates a csv with filepaths and filesizes of all processed audio files. </p>
<p><b>- merge_csv: </b>Merge_csv joins all seperate csv-files.</p>
<p><b>- merge_transcripts_and_wav_files: </b>This module matches the transcripts to the available audio files.</p>
<p><b>- clean_unwanted_characters: </b>Unwanted characters are removed. After cleaning the transcripts, the text is extracted and saved in a txt file which can be used for training the language model.</p>
<p><b>- split_dataset: </b>The final transcripts are splitted into train, test, and dev files and stored in "./final_csv". (train: 75%, test: 15%, dev: 10%)</p>
<p><b>- audio_metrics: </b>After the converter has successfully run, audio metrics and metrics on the subsets of the dataset are provided</p>
<br>
<p><b>Estimation on execution time: </b>full processing with all above modules took 1h 11m for an audio-dataset of 12GB.</p>

## Check characters

<p> In order to compile the necessary language models required by DeepSpeech, the alphabet.txt has to be configured to the generated dataset.</p>
<p> The script check_caracters.py (provided by DeepSpeech) generates a list of characters that appear in the csv-files. It can be instantiated like this: "$ python3 ./Check_Characters/check_characters.py -csv './final_csv/dev.csv' -alpha"</p>
<p> Use this script to check that the text has been cleaned of unwanted characters. If unwanted characters appear, add them to the module "clean_unwanted_characters".</p>

## About this project:

<p>This importer was created as part of the Master Thesis "Automatic Speech Recognition for Swiss German using Deep Neural Networks" for the degree Master of Business Innovation at the University of St. Gallen by Tobias Rordorf. In case of questions please feel free to contact me through <a href='https://www.linkedin.com/in/tobiasrordorf/'>LinkedIn</a>.
