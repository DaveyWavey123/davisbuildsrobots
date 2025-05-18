---
title: "Ising Machine"
date: 2024-04-06
tags: ["computation", "physics", "optimization"]
draft: false
---

<style>
body {
  text-align: left;
}
#matrixSection {
  text-align: center;
  margin-top: 2em;
}
#matrixWrapper {
  display: inline-block;
  margin-top: 30px;
  color: #000;

}
.oscillator {
  width: 50px;
  height: 30px;
  background-color: purple;
  border-radius: 50%;
  margin: 5px auto 2px;
}
table {
  border-collapse: collapse;
  margin: auto;
}
td, th {
  width: 80px;
  height: 80px;
  text-align: center;
  vertical-align: middle;
  border: 2px solid #aaa;
  font-size: 14px;
  cursor: pointer;
  position: relative;
}
.active {
  background-color: #cce5ff;
}
.disabled-cell {
  background-color: #eee;
  pointer-events: none;
}
:root {
  --btn-bg: #337ab7; /* medium blue for light mode */
  --btn-bg-hover: #286090;
}

@media (prefers-color-scheme: dark) {
  :root {
    --btn-bg: #225288;       /* darker blue for dark mode */
    --btn-bg-hover: #183b61;
  }
}

.btn {
  background: var(--btn-bg);
  color: white;
  padding: 0.75rem 1.5rem;
  margin: 2rem 0;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
  transition: background 0.2s ease;
}

.btn:hover {
  background: var(--btn-bg-hover);
}

#sliderContainer {
  margin: 30px auto;
  background: white;
  padding: 15px;
  width: fit-content;
  border-radius: 10px;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.1);
  color: #000;
}
#isingEquation, #outputStates {
  font-family: monospace;
  font-size: 16px;
  margin-top: 30px;
  text-align: center;
}
#calcOutputBtn {
  padding: 10px 20px;
  margin-top: 20px;
  font-size: 16px;
}
</style>

## Disclaimer!

This was truly a MASSIVE undertaking and quite possibly the most technically challenging project that I have ever been a part of. 
As such, I spent an extensive amount of time discussing component selection with experienced PCB design professionals and the theory behind the project with an expert
in quantum mechanics simulation... all to do my (relatively) smaller software integration and hardware simulation in the pictured below.

Truthfully, I am posting this more for what I learned than contributed. For more about this project, please go to Matt Bowring's (the quantum expert) website here: [https://mattbowring.com](https://mattbowring.com).

For my sweeping simplifications, followed by my own professional takeaways, please keep reading!

## The Mission

We built this machine to solve optimization problems by mimicking the behavior of spins in a magnetic material (like in the Ising model). Each spin can be "up" or "down" (represented as +1 or –1), and the goal is to find the lowest-energy arrangement of these spins.

At some level, it is very likely you have already had exposure to this kind of problem and phenomena through popular discussions on quantum mechanics. This is because our goal with this machine is actually to simulate a process called *quantum annealing* through the accessible parts and mechanics of a PCB rather than expensive optical Ising machines.

---

## 📸 Photos

<div style="display: flex; flex-wrap: wrap; gap: 10px; justify-content: center;">

<img src="/images/ising_oscillator.png" alt="Oscillator" style="max-width: 100%; border-radius: 8px;">
Each LC oscillator (an inductor + a capacitor) represents a spin. These oscillators naturally produce oscillating voltages (like sine waves). 
The phase of an oscillator (where in the wave it is — like peak or trough) encodes the spin’s state:
<ul>
  <li>One phase = spin up (+1)</li>
  <li>The opposite phase = spin down (–1)</li>
</ul>
We built our own oscillator to get a super nice, clean sine wave. Pictured above is my LTSpice model we used to help us spec the LC values at the 5MHz we wanted.

<img src="/images/extened-block-diagram.jpg" alt="Extended block diagram" style="max-width: 100%; border-radius: 8px;">
The oscillators are coupled through resistors, so they can influence each other — kind of like magnetic spins pulling each other into alignment (or misalignment) or the way that variables can influence each other in optimization problems.
The sign and strength of the coupling resistors are like the "J" values in the Ising model, which determine whether two spins want to align or not. Pictured above is the logic flow of our components. I went ahead and put it into a draw.io diagram to help with the coding portion later so we could actually encode our problems to be solved.

<img src="/images/ising_equation.png" alt="Ising Equation" style="max-width: 100%; border-radius: 8px;">
What problems can be solved? Anything that can be put into the Ising equation above. Depending on your love of "famous" equations, this may not seem impressive — but that actually includes: logistics & scheduling, vehicle routing, task allocation, portfolio optimization, neural network training (to a limited degree), circuit design and my favorite: qubit error correction (QEC) problems.

Optical versions of these machines have already been used for QEC but — who has that kind of equipment laying around? Again, our goal was to see if these systems could be placed cheaply on a board you or I could use and interface with a computer to do quantum-y stuff. Hopefully now you're appropriately hyped.

<img src="/images/ising_board.png" alt="Final board" style="max-width: 100%; border-radius: 8px;">
The board above uses an ESP32 to communicate with a MAX chip that acts as a FET array for the connections between oscillators. Those oscillators are paired variably via digipots, also receiving input from the ESP32. Lastly, to detect phase and frequency, we used a window detector. And to take in all the different data coming in from that detector I coded cascading shift registers to take the "shotgun" of signals and make them go along one path (also called parallel to serial). If you don't know anything about window detectors, I seriously recommend a quick Google — they solve a lot of key problems in signal processing for a fraction of the cost of an ADC with similar fidelity.

</div>

---

## 🎛️ Ising Coupling Interface

<div id="matrixSection">
  <h2>Ising Coupling Interface</h2>
  <p>Click a box to couple an A oscillator to a B oscillator. (Diagonal pairs disallowed)</p>

  <div id="matrixWrapper">
    <table id="couplingMatrix"></table>
  </div>

  <div id="sliderContainer">
    <label for="resistanceSlider">Set Resistance (kΩ):</label><br><br>
    <input type="range" id="resistanceSlider" min="0" max="10000" step="100" value="1000">
    <br><span id="resistanceValue">1.0</span> kΩ
  </div>

<button id="calcOutputBtn" class="btn">Re-calculate Theoretical Output</button>



  <div id="outputStates"></div>
  <div id="isingEquation">E = 0</div>
</div>

<script>
const N = 4;
const matrix = Array.from({ length: N }, () => Array(N).fill(0));
const resistanceSlider = document.getElementById("resistanceSlider");
const resistanceDisplay = document.getElementById("resistanceValue");
const isingEq = document.getElementById("isingEquation");
const table = document.getElementById("couplingMatrix");
const outputDiv = document.getElementById("outputStates");
const calcButton = document.getElementById("calcOutputBtn");

let A_spins = [1, -1, 1, -1];
let B_spins = [-1, 1, -1, 1];

function updateEquation() {
  let terms = [];
  for (let i = 0; i < N; i++) {
    for (let j = 0; j < N; j++) {
      const val = matrix[i][j];
      if (val !== 0) {
        const Rk = (val / 1000).toFixed(1);
        terms.push(`(1/${Rk})·A${j}·B${i}`);
      }
    }
  }
  isingEq.textContent = "E = " + (terms.length ? terms.join(" + ") : "0");
}

function setCellText(cell, resistance) {
  cell.innerHTML = `<div style="position:absolute;top:50%;left:50%;transform:translate(-50%,-50%)">${(resistance/1000).toFixed(1)}k</div>`;
}

function toggle(i, j, cell) {
  if (i === j) return;
  if (matrix[i][j] === 0) {
    const value = parseInt(resistanceSlider.value);
    matrix[i][j] = value;
    cell.classList.add("active");
    setCellText(cell, value);
  } else {
    matrix[i][j] = 0;
    cell.classList.remove("active");
    cell.innerHTML = "";
  }
  updateEquation();
}

resistanceSlider.addEventListener("input", () => {
  resistanceDisplay.textContent = (resistanceSlider.value / 1000).toFixed(1);
});

calcButton.addEventListener("click", () => {
  let E = 0;
  for (let i = 0; i < N; i++) {
    for (let j = 0; j < N; j++) {
      const R = matrix[i][j];
      if (R !== 0) {
        E += (1 / R) * A_spins[j] * B_spins[i];
      }
    }
  }

  outputDiv.innerHTML = `
    <p>A spins: [${A_spins.join(", ")}]</p>
    <p>B spins: [${B_spins.join(", ")}]</p>
    <p><strong>Calculated Energy: ${E.toFixed(4)} Siemens (1/ohms)</strong></p>
  `;
});

// Build matrix table
const headerRow = document.createElement("tr");
headerRow.appendChild(document.createElement("th"));
for (let j = 0; j < N; j++) {
  const th = document.createElement("th");
  th.innerHTML = `<div class="oscillator"></div><div>A${j}</div>`;
  headerRow.appendChild(th);
}
table.appendChild(headerRow);

for (let i = 0; i < N; i++) {
  const row = document.createElement("tr");
  const th = document.createElement("th");
  th.innerHTML = `<div class="oscillator"></div><div>B${i}</div>`;
  row.appendChild(th);

  for (let j = 0; j < N; j++) {
    const cell = document.createElement("td");
    if (i === j) {
      cell.classList.add("disabled-cell");
    } else {
      cell.addEventListener("click", () => toggle(i, j, cell));
    }
    row.appendChild(cell);
  }
  table.appendChild(row);
}

updateEquation();
</script>

---

## 🎥 Demo Video

<iframe width="560" height="315" src="https://www.youtube.com/embed/REPLACE_ME" frameborder="0" allowfullscreen style="max-width: 100%; border-radius: 10px;"></iframe>

<div>
Hopefully coming soon!
</div>

---

## 🔧 Features

- Spin-based problem solving  
- Low-power analog computing  
- Super Cool
