---
title: "Balancing Bot"
date: 2024-04-06
tags: ["robotics", "control systems"]
draft: false
---

This is my self-balancing robot project, which uses a PID controller and an IMU to stay upright. Below are images and a video walkthrough of the system.

---

## 📸 Photos

<div style="display: flex; flex-wrap: wrap; gap: 10px; justify-content: center;">

<div style="flex: 1; min-width: 250px; text-align: center;">
  <img src="/images/bot1.PNG" alt="Front view" style="max-width: 100%; border-radius: 8px;">
  <p><em>Front view of the robot</em></p>
</div>

<div style="flex: 1; min-width: 250px; text-align: center;">
  <img src="/images/bot2.jpg" alt="Back view" style="max-width: 100%; border-radius: 8px;">
  <p><em>Back view with wiring and power pack</em></p>
</div>

<div style="flex: 1; min-width: 250px; text-align: center;">
  <img src="/images/bot3.jpg" alt="IMU close-up" style="max-width: 100%; border-radius: 8px;">
  <p><em>Close-up of the IMU used for stabilization</em></p>
</div>

</div>

---

## 🎥 Demo Video

<div style="text-align: center;">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/TR29yQAz9zk" 
  frameborder="0" allowfullscreen style="max-width: 100%; border-radius: 10px;"></iframe>
</div>

---

## 🛠️ Features

- Real-time balancing using MPU-6050
- PID tuning in Python
- Arduino + L298N motor control
- 3D-printed frame

---

## 🔗 Source Code

You can find the full code and CAD files here:  
[GitHub Repo](https://github.com/DaveyWavey123/balance_bot)
