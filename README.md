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
Currently Ubuntu 18.04 or greater, and current versions of Raspbian are supported. Credentials for the Ubuntu image are ```ubuntu:rero123.123```

| Platform       | Download Link                                                                                                          |
|----------------|------------------------------------------------------------------------------------------------------------------------|
| Ubuntu x86_64  | [Link](https://drive.google.com/file/d/1OFso_Yd2j98sOgazcZQyldbA2Kf1zUrm/view?usp=sharing) |
| Raspbian (Arm) | [Link](https://drive.google.com/file/d/1tPDe3ijZNglVmJ69kqTY9-QVEZodDpjA/view?usp=sharing)                                                                                                                   |                                                                                            |

A pre-built raspberry pi image is also available [here](https://drive.google.com/file/d/1u1ObydMGRtIR7VOfZv8cEWFiDOaKcc-t/view?usp=sharing). The login is ```pi:rero123.123```.

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

## ROS Usage

The premade distribution is configured to allow for the use of ROS to launch the speech recognition and NLU modules. They can be run with commands

```
roslaunch rero_ros speech_recognition.launch

roslaunch rero_ros nlu.launch
```

Adding an ```&``` to the end of these commands will allow the use of the CLI while still seeing the output from the ROS service. Alternatively, the use of screen can achieve a similar result.

If a ROS service is launched with the ```&``` tag, it must be killed manually. ```ps -a``` will list all processes, and ```kill process_ID_number``` will kill the process.

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

To identify and select an audio input device, run the command ```python3 -m sounddevice```. This will return a list of audio devices and their indicies. Simply change the paDeviceIndex value to the desired index and restart the core server using ```sudo service rerocore restart```.

The model parameters defines the path to the speech recognition model. An optional parameter vocab_path can be set to a file containing a file that defines the available vocabulary to be used for the speech recognition. This feature dramatically increases accuracy when a limited vocabulary is used.

The nlu parameter defines a path to a natural language model. Please see the section on creating a custom NLU model for more information

**IF THE CONFIG FILE IS CHANGED, YOU MUST RESTART THE CORE SERVICE FOR THE CHANGES TO TAKE EFFECT**

```
sudo service rerocore restart
```

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

## Modifying Core Modules
Core modules (speech recognition, NLU, etc.) are written in C++ and can be modified if users want to. The source code can be found in ```~/rero_ros_ws/src/rero_ros/src``` or ```~/rero_ros_ws/src/rero_ros/src``` depending on your image. After changes are made, run the command ```catkin_make``` from the ```~/rero_ros_ws``` or ```~/catkin_ws``` directory depending the version the image you use. This will compile the code and link modules in ROS.

## Licence
The ReroCore distributables are licenced under the [GPL-2.0 License](https://github.com/reverbrobotics/rero_core_dist/blob/master/LICENSE). Commercial licensing under a separate license is available on request.

## Citations
If you use this project in your research, please consider citing our conference paper that outlines the project.

```
@article{grasse_tata_2021_platform, 
            title={An End-to-End Platform for Human-Robot Speech Interaction}, 
            url={https://r00binson.wixsite.com/soundinhri/submissions}, 
            journal={Sound in HRI Workshop. 16th Annual Conferencefor Basic and Applied Human-Robot Interaction.}, 
            author={Grasse, Lukas S. and Tata, Matthew S.}, 
            year={2021}, month={Mar}
}
```
