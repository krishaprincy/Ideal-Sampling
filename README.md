# IDEAL, NATURAL, FLAT-TOP SAMPLING


# AIM: 
Write a simple Python program for the construction and reconstruction of ideal, natural, and flattop sampling.

# TOOLS REQUIRED:
  Google Colab 

# THEORY: 

Alright — here’s a **clean LAB RECORD–READY theory** (neatly structured, not too long, exactly what examiners expect). You can copy this directly.

---

# 📘 **Experiment: Sampling of Signals (Ideal, Natural and Flat-top Sampling)**

## 🔹 **Aim**

To study and analyze different types of sampling techniques: **Ideal sampling, Natural sampling, and Flat-top sampling**, and to observe their waveforms and reconstruction.

---

## 🔹 **Theory**

### 🔸 **Sampling**

Sampling is the process of converting a **continuous-time signal** into a **discrete-time signal** by taking values at regular intervals.

**1. Ideal Sampling (Impulse Sampling)**
Ideal sampling is obtained by multiplying the signal with an **impulse train**.

**Mathematical Expression:**

[x_s(t) = x(t)\sum_{n=-\infty}^{\infty} \delta(t - nT_s)]


* Samples are taken at discrete instants
* Output consists of **impulses (spikes)**
* Amplitude equals signal value at sampling instant
* It is **not physically realizable**


**2. Natural Sampling**

Natural sampling is obtained by multiplying the signal with a **periodic pulse train of finite width**.

### Mathematical Expression:

[
x_s(t) = x(t) \cdot p(t)
]

### Explanation:

* Signal is passed only during pulse duration
* Output contains **small portions of waveform**
* Amplitude varies naturally within each pulse

### Key Point:

* More practical than ideal sampling
* Less distortion compared to flat-top sampling

---

## 🔹 **3. Flat-top Sampling (Sample-and-Hold)**

Flat-top sampling is a process where the sampled value is **held constant for a certain time**.

### Mathematical Expression:

[
x_s(t) = \sum x(nT_s), rect\left(\frac{t-nT_s}{\tau}\right)
]

### Explanation:

* Each sample is held constant
* Output appears as **staircase waveform**
* Implemented using **Sample-and-Hold circuit**

### Key Point:

* Most widely used in practical systems
* Introduces **aperture effect distortion**

---

## 🔹 **Nyquist Sampling Theorem**

To avoid aliasing:
[
f_s \geq 2f_m
]

Where:

* ( f_s ) = Sampling frequency
* ( f_m ) = Maximum signal frequency

---

## 🔹 **Comparison**

| Type     | Output           | Practical |
| -------- | ---------------- | --------- |
| Ideal    | Impulses         | No        |
| Natural  | Chopped waveform | Limited   |
| Flat-top | Staircase        | Yes       |

---

## 🔹 **Result**

The different sampling techniques were successfully studied. The waveforms of **ideal, natural, and flat-top sampling** along with their reconstructed signals were observed and analyzed.

---

If you want, I can also give:
✔ **Procedure (step-by-step for lab record)**
✔ **Apparatus/Software used**
✔ **Viva questions (very important before submission)**

  
# PROGRAM:

# Ideal Sampling: 

```
import numpy as np
import matplotlib.pyplot as plt

# Sampling frequency
fs = 100   # Hz

# Time axis (1 second)
t = np.arange(0, 1, 1/fs)

# Signal frequency
f = 5   # Hz

# Sampled signal (impulse samples of sine wave)
signal_sampled = np.sin(2 * np.pi * f * t)

# Plot impulse sampling (stem plot)
plt.figure(figsize=(10, 4))

plt.stem(t, signal_sampled, 
         linefmt='r-',      # red vertical lines
         markerfmt='ro',    # red dots
         basefmt='r-',      # base line
         label='Sampled Signal (fs = 100 Hz)')

plt.title('Sampling of Continuous Signal (fs = 100 Hz)')
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
from scipy.signal import butter, lfilter

# Parameters
fs, T, fm, fp = 1000, 1, 5, 50
t = np.arange(0, T, 1/fs)

# Message signal
m = np.sin(2*np.pi*fm*t)

# Pulse train
pw = fs // (2*fp)
p = np.zeros_like(t)
p[::fs//fp] = 1
p = np.convolve(p, np.ones(pw), mode='same')

# Natural sampling
nat = m * p

# Reconstruction (LPF)
b, a = butter(4, 10/(0.5*fs), 'low')
rec = lfilter(b, a, nat)

# Plot
plt.figure(figsize=(10,9))


plt.subplot(4,1,1)
plt.plot(t, m)
plt.title("Message Signal")
plt.grid(True)

plt.subplot(4,1,2)
plt.plot(t, p)
plt.title("Pulse Train")
plt.grid(True)

plt.subplot(4,1,3)
plt.plot(t, nat)
plt.title("Natural Sampling")
plt.grid(True)

plt.subplot(4,1,4)
plt.plot(t, rec, color='g')
plt.title("Reconstructed Signal")
plt.grid(True)

plt.tight_layout(rect=[0,0,1,0.93])
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

<img width="901" height="429" alt="image" src="https://github.com/user-attachments/assets/80965c56-188f-4879-b3a8-7da2d643feab" />


# Natural Sampling: 

<img width="998" height="839" alt="image" src="https://github.com/user-attachments/assets/105a8a5c-5901-4c7d-ae39-e4caf47c453f" />


# Flat-top sampling: 

<img width="1224" height="1039" alt="image" src="https://github.com/user-attachments/assets/37f3dac9-05ef-4030-aad5-b14013bee1c9" />


# RESULTS: 

Impulse sampling gives perfect reconstruction, while natural and flat-top sampling allow approximate reconstruction, with flat-top introducing slight amplitude distortion.








































































.
