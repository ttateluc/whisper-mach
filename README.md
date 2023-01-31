# Whisper-Mach
Transcriptions of 100+ audio files from prestigious/abstract artist curated from form of skeleton OpenAI-Whisper API, thereby delivering clarity to an enjoyable niche, with 90%+ initial acc, far exceeding time constraints of ear-to-keystroke translations predating the method. Not monetized out of respect for artist's standings.

[![Animation](https://user-images.githubusercontent.com/97220909/215906350-1a72ede8-030c-4961-b1fe-d27ca268f698.mp4)](https://user-images.githubusercontent.com/97220909/215907372-016868b0-985d-4760-adbe-4dbfb36aaea6.mp4)
## What is Whisper?



Whisper is an automatic State-of-the-Art speech recognition system from OpenAI that has been trained on 680,000 hours 
of multilingual and multitask supervised data collected from the web. This large and diverse 
dataset leads to improved robustness to accents, background noise and technical language. In 
addition, it enables transcription in multiple languages, as well as translation from those 
languages into English. OpenAI released the models and code to serve as a foundation for building useful
applications that leverage speech recognition. 

## How to use Whisper

The whisper model is available on GitHub. You can download it with the following command directly in the Jupyter notebook:

```python
!pip install git+https://github.com/openai/whisper.git 
```

Whisper needs `ffmpeg` installed on the current machine to work. Maybe you already have it installed, 
but its likely your local machine needs this program to be installed first. 

OpenAI refers to multiple ways to install this package, but we will be using the Scoop package manager.
 [Here](https://www.wikihow.com/Install-FFmpeg-on-Windows) is a tutorial
how to do it manually

In the Jupyter Notebook you can install it with the following command:

```bash
irm get.scoop.sh | iex
scoop install ffmpeg
```

After the installation a restart of is required if you are using your local machine.

Now we can continue. Next we import all necessary libraries:

```python
import whisper
import os
import numpy as np
import torch
```

Using a GPU is the preferred way to use Whisper. If you are using a local machine, you can check 
if you have a GPU available. 
The first line results `False`, if Cuda compatible Nvidia GPU is not available and `True` if it 
is available. The second line of 
code sets the model to preference GPU whenever it is available.

```python
torch.cuda.is_available()
DEVICE = "cuda" if torch.cuda.is_available() else "cpu"
```

Now we can load the Whipser model. The model is loaded with the following command:

```python
model = whisper.load_model("base", device=DEVICE)
print(
    f"Model is {'multilingual' if model.is_multilingual else 'English-only'} "
    f"and has {sum(np.prod(p.shape) for p in model.parameters()):,} parameters."
)
```

Please keep in mind, that there are multiple different models available. You can find all of them 
[here](https://github.com/openai/whisper/blob/main/model-card.md).
Each one of them has tradeoffs between accuracy and speed (compute needed). We will use the 
'base' model for this tutorial.

Next you need to load your audio file you want to transcribe. 

```python
audio = whisper.load_audio("audio.mp3")
audio = whisper.pad_or_trim(audio)
mel = whisper.log_mel_spectrogram(audio).to(model.device)
```

The `detect_language` function detects the language of your audio file:

```python
_, probs = model.detect_language(mel)
print(f"Detected language: {max(probs, key=probs.get)}")
```

We transcribe the first 30 seconds of the audio using the DecodingOptions and the decode command. 
Then print out the result:

```pyhton
options = whisper.DecodingOptions(language="en", without_timestamps=True, fp16 = False)
result = whisper.decode(model, mel, options)
print(result.text)
```

Next we can transcribe the whole audio file.
  
```python
result = model.transcribe("../input/audiofile/audio.mp3")
print(result["text"])
```

This will print out the whole audio file transcribed, after the execution has finished.
