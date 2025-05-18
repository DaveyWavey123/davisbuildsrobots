---
title: "DIY EEG"
date: 2024-04-06
tags: ["biosignals", "EEG", "neurotech"]
draft: false
---

This is my DIY EEG project, built using the AD627B instrumentation amplifier and TL084CN op-amp to capture brainwave signals. The circuit is designed to amplify weak scalp-level voltages and filter out common noise, including 60 Hz powerline interference.

---

## 📸 Photos

<div style="display: flex; flex-wrap: wrap; gap: 10px; justify-content: center;">

<div style="flex: 1; min-width: 250px; text-align: center;">
  <img src="/images/forehead.jpg" alt="EEG electrode setup" style="max-width: 100%; border-radius: 8px;">
  <p><em>Two electrodes placed on the Fp1 and Fp2 to track general attention/focus.</em></p>
</div>

<div style="flex: 1; min-width: 250px; text-align: center;">
  <img src="/images/hospital.jpg" alt="Hospital" style="max-width: 100%; border-radius: 8px;">
  <p><em>Fun fact theres are actually electro cardiagram pads from when I went to the hospital for Rhabdo!</em></p>
</div>

<div style="flex: 1; min-width: 250px; text-align: center;">
  <img src="/images/eeg.png" alt="Circuit board" style="max-width: 100%; border-radius: 8px;">
  <p><em>EEG amplifier circuit based on AD627B and TL084CN</em></p>
</div>

<div style="flex: 1; min-width: 250px; text-align: center;">
  <img src="/images/eeg3.jpg" alt="Signal output" style="max-width: 100%; border-radius: 8px;">
  <p><em>Captured alpha wave signal shown on oscilloscope</em></p>
</div>

</div>

---

## 🎥 Demo Video

<div style="text-align: center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/YOUR_VIDEO_ID" 
  frameborder="0" allowfullscreen style="max-width: 100%; border-radius: 10px;"></iframe>
</div>
Coming Soon!

---

## 🧠 Features

- AD627B instrumentation amplifier for clean differential input
- TL084CN quad op-amp used for high-pass, low-pass, and notch filters
- Signal observable on oscilloscope or digitized via ADC

---

## 🔗 Source Files

You can find the full schematic, SPICE simulation files, and build guide here:  
[GitHub Repo](https://github.com/YourUsername/diy-eeg)
