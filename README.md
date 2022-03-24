# AutonomousAlert
Autonomous Siren Alert System

This repository contains code used to develop an *autonomous siren detection system to be deployed on a Raspbery Pi 4.* 

The design involves the use of an *18 Layer Residual Neural Network (ResNet18)*, to train and implement a model capable of distinguishing ambient noise from emergency vehicle sirens based on a spectrogram input taken from an input microphone.
Data was collected by compiling 5 second .mp3 recordings, in which approximately 200 samples contained Ambulance sirens masked with ambient and urban noise, and 200 samples contained solely ambient and urban noise.

The model training process was as follows:

1. The samples were collected and organized according to their sound contents, stored under either `Amulance_spec` or `Other_spec`.
2. `GetSpec.ipynb` was used to convert the 400 samples into spectrograms, limited form 20Hz to 20kHz (approximate range of human hearing) and plotted on a logarithmic scale (since humans perceive sound level logarithmically).
3. These spectrograms were exported to google drive, and used in `TrainModel.ipynb` to train the model. The `fastai` library was used.
4. After fine tuning for optimal accuracy, the model was exported to a .pkl file to be later deployed onto the Raspberry Pi.

`SirenAlertPC.ipynb` contains proof-of-concept or testing code to confirm the performance of the model. It is essentially a mock-up of the model's functioning on the Pi. The code takes a 5 second microphone input from the PC mic, produces a spectrogram, and then passes this spectrogram to the model to produce a siren prediction. After every second, the spectrogram is updated with a new second of sound data and the first second is discarded, as to have the spectrogram be updated every second with new input data. After the model is exported to the Pi, the intended flow is as follows:

1. Pi takes 5 second microphone input (buffer).
2. Spectrogram of initial buffer is produced.
3. Pi takes a 1 second mic input, creates a spectrogram, and concatenates this to the end of the buffer. 
4. First second of the buffer is discarded.
5. Final spectrogram is passed to the model (now imported onto the Pi) and a prediction is made.
6. If the prediction indicates the presence of a siren, the Pi enables a pre-defined GPIO pin to drive a series of LED lights as an output.
7. Otherwise, if there is no siren, the Pi does nothing.
8. The process of adding a new second of data and making a new prediction is repeated indefinitely, or until the unit is powered off.
