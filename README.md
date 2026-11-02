# bayes-localisation

A discrete Bayes (histogram) filter for one-dimensional robot localisation, implemented with NumPy. The notebook includes simulation experiments on localisation accuracy and on the calibration of credible regions. The method follows Thrun, Burgard and Fox (2005), *Probabilistic Robotics*, chapter 4.

## Contents

- `bayes_localisation.ipynb`: implementation and experiments
- `figures/`: figures produced by the notebook
- `requirements.txt`: dependencies

## Experiments

1. Measurement update on three cells, checked against a hand calculation.
2. Measurement and motion updates on a loop of 10 cells with a binary door/wall sensor.
3. Localisation in a 20 m corridor with a range sensor and noisy motion.
4. Calibration of the 90% credible region under correct and incorrect noise parameters.

## Results

On the 10-cell loop, the belief concentrated around door cells but did not converge to a single cell. Over 300 runs, the most probable cell was the true cell about one third of the time (chance level 10%), because motion noise adds uncertainty at each step and a door/wall reading is weakly informative.

![Belief on the 10-cell loop](figures/discrete_filter.png)

In the corridor, the range sensor does not identify which landmark it observes, so the initial belief is multimodal. Motion resolves the ambiguity. In the example run, more than 90% of the belief lay within 0.5 m of the best estimate by step 8, and the final error was 0.16 m.

![Belief over position, entropy and error](figures/range_filter.png)

Calibration was evaluated over 300 simulated runs. At the final step, the credible region was the smallest set of cells holding 90% of the belief, and coverage was the fraction of runs in which the true position lay inside it. The same data were filtered with three assumptions about the noise.

| Assumed noise (sensor, motion) | Coverage of the 90% region | Mean region width |
|---|---|---|
| 0.10 m, 0.05 m (too small) | 53% (s.e. 2.9) | 0.24 m |
| 0.25 m, 0.15 m (true values) | 92% (s.e. 1.6) | 0.57 m |
| 0.60 m, 0.40 m (too large) | 99% (s.e. 0.7) | 1.25 m |

Standard errors are in percentage points.

![Calibration](figures/calibration.png)

## Limitations

- The simulated noise matches the filter's assumed model, so the matched-noise result is a best case. Non-Gaussian errors, outliers, map errors and kidnapped-robot events were not tested.
- The map and the motion commands are known. The method performs localisation only.
- The number of cells grows exponentially with the state dimension. Particle filters and Kalman filters are used for larger problems.
- Calibration was evaluated at the final step only.

## Usage

```
pip install -r requirements.txt
jupyter notebook bayes_localisation.ipynb
```

Random seeds are fixed, so the reported results are reproducible. The notebook saves its figures to `figures/`.

## Reference

Thrun, S., Burgard, W. and Fox, D. (2005) *Probabilistic Robotics*. Cambridge, MA: MIT Press.
