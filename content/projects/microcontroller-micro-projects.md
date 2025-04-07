---
title: "Microcontroller Micro Projects"
date: 2024-04-06
tags: ["micropython", "wokwi", "embedded"]
draft: false
---

Quick MicroPython experiments with full code, embedded simulators, and source links.

---

## 🧲 1. Wokwi Debounce Demo

This project uses MicroPython to debounce a pushbutton and toggle an LED — ideal for learning interrupts and hardware signal smoothing.

🎮 **Live Demo (click + edit):**

<div style="text-align: center;">
  <iframe width="100%" height="500" src="https://wokwi.com/projects/372153001566564353/embed" frameborder="0" allowfullscreen style="border-radius: 10px;"></iframe>
</div>

🧠 **MicroPython Code:**

```python
from machine import Pin
from time import sleep

led = Pin(2, Pin.OUT)
btn = Pin(4, Pin.IN, Pin.PULL_UP)

while True:
    if not btn.value():
        led.value(not led.value())
        sleep(0.3)  # debounce delay
