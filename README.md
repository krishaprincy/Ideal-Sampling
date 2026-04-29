# IDEAL, NATURAL, FLAT-TOP SAMPLING


# AIM: 
Write a simple Python program for the construction and reconstruction of ideal, natural, and flattop sampling.

# TOOLS REQUIRED:
  Google Colab 
  
# PROGRAM:

# Ideal Sampling: 

```
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import resample

# Sampling frequency
fs = 50

# Time vector 
t = np.arange(0, 1, 1/fs)

# Signal frequency
f = 5  

# Continuous signal (sine wave)
signal = np.sin(2 * np.pi * f * t)

# 1. Plot Continuous Signal

plt.figure(figsize=(10, 4))
plt.plot(t, signal, label='Continuous Signal')
plt.title('Continuous Signal (fs = 100 Hz)')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()

# 2. Impulse Sampling 

t_sampled = np.arange(0, 1, 1/fs)
signal_sampled = np.sin(2 * np.pi * f * t_sampled)

plt.figure(figsize=(10, 4))
plt.stem(t_sampled, signal_sampled, linefmt='r-', markerfmt='ro', basefmt='r-', 
         label='Sampled Signal (fs = 100 Hz)')
plt.title('Impulse Sampling of Signal')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()

# 3. Reconstruction of Signal

reconstructed_signal = resample(signal_sampled, len(t))

plt.figure(figsize=(10, 4))
plt.plot(t, reconstructed_signal, 'r--', label='Reconstructed Signal')
plt.title('Reconstructed Signal from Samples')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()
```



# Natural Sampling: 
```
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import resample

# Sampling frequency
fs = 100  

# Time vector
t = np.arange(0, 1, 1/fs)

# Signal frequency
f = 5  

# Continuous signal
signal = np.sin(2 * np.pi * f * t)

# 1. Plot Continuous Signal
plt.figure(figsize=(10, 4))
plt.plot(t, signal, label='Continuous Signal')
plt.title('Continuous Signal (fs = 100 Hz)')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()

# 2. Natural Sampling

# Pulse width 
pulse_width = int(0.2 * fs)  

# Create pulse train
pulse_train = np.zeros_like(t)

for i in range(0, len(t), int(fs/f)):
    pulse_train[i:i+pulse_width] = 1

# Natural sampled signal 
natural_sampled = signal * pulse_train

plt.figure(figsize=(10, 4))
plt.plot(t, natural_sampled, 'r', label='Natural Sampled Signal')
plt.title('Natural Sampling')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()

# 3. Reconstruction

reconstructed_signal = resample(natural_sampled, len(t))

plt.figure(figsize=(10, 4))
plt.plot(t, reconstructed_signal, 'g--', label='Reconstructed Signal')
plt.title('Reconstructed Signal from Natural Sampling')
plt.xlabel('Time [s]')
plt.ylabel('Amplitude')
plt.grid(True)
plt.legend()
plt.show()
```




# Flat-top sampling: 
```
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

# Parameters
fs = 1000        
T = 1            
t = np.arange(0, T, 1/fs)

fm = 5           

# Original signal
message_signal = np.sin(2 * np.pi * fm * t)

# Sampling
pulse_rate = 50
pulse_indices = np.arange(0, len(t), int(fs / pulse_rate))

# Flat-top signal initialization
flat_top_signal = np.zeros_like(t)

# Pulse width 
pulse_width = int(fs / (2 * pulse_rate))

# Flat-top sampling 
for idx in pulse_indices:
    if idx + pulse_width < len(t):
        flat_top_signal[idx:idx + pulse_width] = message_signal[idx]

# Low-pass filter 
def lowpass_filter(signal, cutoff, fs, order=5):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff / nyquist
    b, a = butter(order, normal_cutoff, btype='low')
    return lfilter(b, a, signal)

cutoff_freq = 2 * fm
reconstructed_signal = lowpass_filter(flat_top_signal, cutoff_freq, fs)

# Plotting
plt.figure(figsize=(12, 10))

# Original signal
plt.subplot(4,1,1)
plt.plot(t, message_signal)
plt.title("Original Signal")

# Sampling instants
plt.subplot(4,1,2)
plt.stem(t[pulse_indices], message_signal[pulse_indices], basefmt=" ")
plt.title("Sampling Instants")

# Flat-top sampled signal
plt.subplot(4,1,3)
plt.plot(t, flat_top_signal)
plt.title("Flat-top Sampled Signal")

# Reconstructed signal
plt.subplot(4,1,4)
plt.plot(t, reconstructed_signal)
plt.title("Reconstructed Signal")

plt.tight_layout()
plt.show()

```


# OUTPUT WAVEFORM: 

# Ideal Sampling: 

<img width="925" height="1186" alt="image" src="https://github.com/user-attachments/assets/77bd2020-b313-4413-b3a5-4f7c6cdc2078" />

# Natural Sampling: 

<img width="914" height="1181" alt="image" src="https://github.com/user-attachments/assets/eed274c1-0fb1-4136-b04a-c1ede2e4d2ca" />

# Flat-top sampling: 

<img width="1224" height="1039" alt="image" src="https://github.com/user-attachments/assets/37f3dac9-05ef-4030-aad5-b14013bee1c9" />


# RESULTS: 

Impulse sampling gives perfect reconstruction, while natural and flat-top sampling allow approximate reconstruction, with flat-top introducing slight amplitude distortion.








































































.
