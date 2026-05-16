# Pulse-Code-Modulation
# Aim
Write a simple Python program for the modulation and demodulation of PCM, and DM.
# Tools required
# Program

PCM:
```
# PCM - Pulse Code Modulation

import numpy as np
import matplotlib.pyplot as plt

# Parameters
sampling_rate = 5000      # Sampling rate
frequency = 50            # Message signal frequency
duration = 0.1            # Duration
quantization_levels = 16 # Number of quantization levels

# Generate time vector
t = np.linspace(0, duration, int(sampling_rate * duration), endpoint=False)

# Generate analog message signal
message_signal = np.sin(2 * np.pi * frequency * t)

# Generate clock signal
clock_signal = np.sign(np.sin(2 * np.pi * 200 * t))

# Quantization
quantization_step = (max(message_signal) - min(message_signal)) / quantization_levels

quantized_signal = (
    np.round(message_signal / quantization_step)
    * quantization_step
)

# PCM digital signal
pcm_signal = (
    (quantized_signal - min(quantized_signal))
    / quantization_step
)

pcm_signal = pcm_signal.astype(int)

# Plotting
plt.figure(figsize=(12, 10))

# Message Signal
plt.subplot(4, 1, 1)
plt.plot(t, message_signal, color='blue')
plt.title("Message Signal (Analog)")
plt.xlabel("Time [s]")
plt.ylabel("Amplitude")
plt.grid(True)

# Clock Signal
plt.subplot(4, 1, 2)
plt.plot(t, clock_signal, color='green')
plt.title("Clock Signal")
plt.xlabel("Time [s]")
plt.ylabel("Amplitude")
plt.grid(True)

# PCM Modulated Signal
plt.subplot(4, 1, 3)
plt.step(t, quantized_signal, color='red')
plt.title("PCM Modulated Signal")
plt.xlabel("Time [s]")
plt.ylabel("Amplitude")
plt.grid(True)

# PCM Demodulated Signal
plt.subplot(4, 1, 4)
plt.plot(
    t,
    quantized_signal,
    color='purple',
    linestyle='--'
)

plt.title("PCM Demodulation Signal")
plt.xlabel("Time [s]")
plt.ylabel("Amplitude")
plt.grid(True)

plt.tight_layout()
plt.show()
```
DM:
~~~
# Delta Modulation

import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, filtfilt

# Parameters
fs = 10000      # Sampling frequency
f = 10          # Signal frequency
T = 1           # Duration
delta = 0.1     # Step size

# Time vector
t = np.arange(0, T, 1/fs)

# Message signal
message_signal = np.sin(2 * np.pi * f * t)

# Delta Modulation Encoding
encoded_signal = []
dm_output = [0]

prev_sample = 0

for sample in message_signal:

    if sample > prev_sample:
        encoded_signal.append(1)
        dm_output.append(prev_sample + delta)

    else:
        encoded_signal.append(0)
        dm_output.append(prev_sample - delta)

    prev_sample = dm_output[-1]

# Delta Demodulation
demodulated_signal = [0]

for bit in encoded_signal:

    if bit == 1:
        demodulated_signal.append(
            demodulated_signal[-1] + delta
        )

    else:
        demodulated_signal.append(
            demodulated_signal[-1] - delta
        )

# Convert to numpy array
demodulated_signal = np.array(demodulated_signal)

# Low-pass filter
def low_pass_filter(signal, cutoff_freq, fs, order=4):

    nyquist = 0.5 * fs
    normal_cutoff = cutoff_freq / nyquist

    b, a = butter(
        order,
        normal_cutoff,
        btype='low',
        analog=False
    )

    return filtfilt(b, a, signal)

# Filtered signal
filtered_signal = low_pass_filter(
    demodulated_signal,
    cutoff_freq=20,
    fs=fs
)

# Plotting
plt.figure(figsize=(12, 6))

# Original Signal
plt.subplot(3, 1, 1)
plt.plot(t, message_signal, linewidth=1)
plt.title("Original Signal")
plt.grid()

# Delta Modulated Signal
plt.subplot(3, 1, 2)
plt.step(
    t,
    dm_output[:-1],
    where='mid'
)

plt.title("Delta Modulated Signal")
plt.grid()

# Demodulated Signal
plt.subplot(3, 1, 3)
plt.plot(
    t,
    filtered_signal[:-1],
    linestyle='dotted',
    linewidth=1,
    color='r'
)

plt.title("Demodulated & Filtered Signal")
plt.grid()

plt.tight_layout()
plt.show()
~~~
# Output Waveform
PCM:

<img width="1189" height="990" alt="image" src="https://github.com/user-attachments/assets/009d7c53-258d-49d8-af9e-74a81fb157f3" />

DM:

<img width="1189" height="590" alt="image" src="https://github.com/user-attachments/assets/2ec9242d-6643-4c81-a646-402f1ec52c37" />


# Results

Thus,the PCM AND DC is verified succesfully
