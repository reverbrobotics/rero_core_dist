# Rero Core Distributables

This repository contains the distributables for ReroCore. They can be downloaded in the releases section or from the links in the table below. 

## Dependencies
This distributable  requires Alsa and [PortAudio](http://files.portaudio.com/download.html) to be installed. First, install alsa and the alsa shared library using the following command
```
sudo apt-get install libasound2-dev
```

Next, you can download, compile, and install the latest version of portaudio by running the following commands.


```
wget http://files.portaudio.com/archives/pa_stable_v190700_20210406.tgz
tar -zxvf pa_stable_v190700_20210406.tgz
cd portaudio/
./configure --with-alsa
make
sudo make install
```

## Download
Currently Ubuntu 18.04 or greater, and current versions of Raspbian are supported. 

| Platform       | Download Link                                                                                                          |
|----------------|------------------------------------------------------------------------------------------------------------------------|
| Ubuntu x86_64  | [Link](https://github.com/reverbrobotics/rero_core_dist/releases/download/v0.2.0/ReroCore-0.2.0_ubuntu_x86_64.tgz) |
| Raspbian (Arm) | [Link](https://github.com/reverbrobotics/rero_core_dist/releases/download/v0.2.0/ReroCore-0.2.0-ARMv7.tgz)                                                                                                                   |                                                                                            |

A pre-built raspberry pi image is also available [here](https://drive.google.com/file/d/1JYbGrKC-MNqzVTV_mwpuhE2ufbwLCx7V/view?usp=sharing). The login is ```pi:rero123.123```.

## Usage
To use ReroCore, simply extract the archive and run the executables with the provided configuration file. Executables take the path to a config.ini file as their first agument, e.g.

```
/path/to/executable /path/to/config.ini
```

### Starting the Server
To launch the ReroCore server, from the extracted directory run:

```
./bin/rero_server ./conf/config.ini
```

If the server started successfully you should eventually see the line ```Server listening on 0.0.0.0:50052```

### Testing the Speech Recognition
Next you can test the speech recognition by running

```
./bin/speech_recognition_cmdline ./conf/config.ini
```
and speaking into your microphone. This will print the speech recognition results to the terminal window. 

The speech recognition performance can make use of a custom vocabulary to improve accuracy for domain-specific recognition. Please see the "Defining a Custom Speech Recognition Vocabulary" section for more details.

### Testing the Natural Language Understanding (NLU)
You can test the NLU by running the following command:

```
./bin/nlu_cmdline ./conf/config.ini
```

then you will be prompted to type in a test sentence. The default NLU model parses requests to requests to play music, such as the request "Please play the Beatles".

### Testing Raw Audio Playback

To listen to the raw audio coming from the microphones, run the command

```
./bin/audio_player ./conf/config.ini
```

Headphones are recommended in order to prevent feedback/echoes.

## Configuration

The default config.ini contains the following parameters

```
[server]
host=0.0.0.0
port=50052
paDeviceIndex=-1

[model]
path="./models/vosk-rpi-model/"

[nlu]
model_path = "./models/nlu/nlu_engine_music/"
```

The server parameters define the gRPC host, port, and PortAudio device index. A device index of -1 signifies that the system should use the default device.

The model parameters defines the path to the speech recognition model. An optional parameter vocab_path can be set to a file containing a file that defines the available vocabulary to be used for the speech recognition. This feature dramatically increases accuracy when a limited vocabulary is used.

The nlu parameter defines a path to a natural language model. Please see the section on creating a custom NLU model for more information

## Defining a Custom Speech Recognition Vocabulary
The speech recognition accuracy can be easily improved by using a custom restricted vocabulary that only contains domain-specific words. To make use of this feature, simply create a vocabulary text file that contains each vocabulary entry on a new line, and then add the vocabulary file to the configuration.

For example, set the model section of the config.ini to
```
[model]
path="./models/vosk-rpi-model/"
vocab_path="./models/vocab.txt"
```

where vocab_path points to a ```vocab.txt``` file containing the custom vocabulary. If using the raspberry pi image, the system service must be restarted using 

```
sudo service rerocore restart
```

for the new vocabulary file to take effect. 


## Creating a custom NLU model
A custom NLU model can be created by following the steps outlined in this [github repo](https://github.com/snipsco/snips-nlu). Simply follow the instructions in the **Command Line Interface** section to train a new NLU model, and then configure the model by setting the nlu section of the config.ini to
```
[nlu]
model_path = "/path/to/trained/model"
```
*n.b. Models are compatible across architectures, so it is usually faster/more convenient to first train the custom NLU model on a x86 machine, and then copy the trained model onto arm-based systems such as the raspberry pi for inference.*

## Licence
The ReroCore distributables are licenced under the [GPL-2.0 License](https://github.com/reverbrobotics/rero_core_dist/blob/master/LICENSE). 

## Citation
If you use this project in your research, please cite our conference paper that outlines the project.

```
@article{grasse2021platform, 
          title={An End-to-End Platform for Human-Robot Speech Interaction}, 
          url={https://r00binson.wixsite.com/soundinhri/submissions}, 
          journal={Sound in HRI Workshop. 16th Annual Conference for Basic and Applied Human-Robot Interaction.}, 
          author={Grasse, Lukas S. and Tata, Matthew S.}
}
```
