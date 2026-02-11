# Phase 1: Extracting Points from Image

## Tools:

- [Engauge Digitizer](https://akhuettel.github.io/engauge-digitizer/)

## Procedure:

1. Install the software using your package manager. On CachyOS (Arch): Install
   it from the AUR.

```fish
paru -S aur/engauge
```

2. Open the software
3. Import the Image
4. Mark at least three points for the 2D Coordinate System
5. Go to Settings -> Color Filter
6. Since the curve to digitize is just a different color from the grid, use hue.
7. Limit the range to blue.
8. Digitize the curve.
9. Export the points to a CSV file.
10. Repeat for the remaining images.

# Phase 2: System Identification (Optimization)

## Tools:

- Python 3.12+
- Libraries: `pandas` (data), `scipy` (optimization), `numpy` (math),
  `matplotlib` (viz)

## Procedure:

1. **Environment Setup**: Create a clean virtual environment to avoid system
   package conflicts.

```fish
python -m venv .venv
source .venv/bin/activate.fish
pip install pandas scipy numpy matplotlib jupyterlab
```

2. **Data Stitching**:
   - The dataset is split into "high frequency" (0-1s) and "low frequency"
     (1-5s) components.
   - **Action**: Load `0to1.csv` and `1to5.csv` using pandas. Concatenate them
     into a single dataframe to ensure the optimizer sees both the sharp
     transient rise and the slow steady-state settling.
   - _Note_: `0to5.csv` was ignored due to redundant sampling range.

3. **Model Definition**:
   - Define the non-linear transfer function for a 3-time-constant system:
     $$
     c(t) = K (1 - Ae^{-t/\tau_1} - Be^{-t/\tau_2} - Ce^{-t/\tau_3})
     $$

4. **Non-Linear Least Squares Fit**:
   - Algorithm: Levenberg-Marquardt
   - Initial Guesses (`p0`) to avoid local minima in the error surface.
     - $K \approx 4.0$ (Visual inspection of asymptote).
     - $\tau_{1,2,3} \approx [0.1, 0.5, 2.0]$ (Visual estimation of "knees" in
       the curve).
   - Constraints: Enforce positive values for Time Constants and Gain.

5. Execution & Extraction:
   - Run the optimization loop.
   - Extract the minimized parameters ($K, \tau_n, A, B, C$).

## Results:

- **Steady State ($K$)**: $\approx 4.0004$ V
- **Time Constants ($\tau$)**:
  - $\tau_1 \approx 0.0941s$ (Fast process)
  - $\tau_2 \approx 0.3255s$ (Medium process)
  - $\tau_3 \approx 0.9951s$ (Slow process)
