# 1D Optical Lithography Simulator

A one-dimensional optical lithography simulator in Python. It follows a 50 % duty-cycle line-and-space mask from its diffraction orders, through an ideal lens pupil, to an aerial image, and finally to a printed critical dimension (CD) in positive photoresist.

Coursework for **F437 Semiconductor Fabrication Technology** (Process Simulation Assignment 2), BITS Pilani K K Birla Goa Campus. Group 7, **Case C**.

## What it demonstrates

1. A lens accepts only some diffraction orders (hard-pupil cutoff).
2. The accepted orders interfere to form the aerial image.
3. Defocus changes the relative phase of the orders, and therefore the image.
4. A photoresist threshold converts intensity and dose into a printed CD.

## Model

| Step | Equation |
|---|---|
| Mask spectrum (f = 0.5) | $c_0 = f,\quad c_m = \dfrac{\sin(\pi m f)}{\pi m}$ |
| Pupil cutoff | accept order $m$ if $\lvert m\rvert\lambda/p \le NA$, so $m_{max} = \lfloor NA\,p/\lambda \rfloor$ |
| Complex field | $A(x,\Delta z) = \sum_{m=-m_{max}}^{m_{max}} c_m\, e^{i2\pi m x/p}\, e^{-i\pi\lambda\Delta z\,(m/p)^2}$ |
| Intensity | $I = \lvert A\rvert^2$, normalised by the same best-focus peak: $I_N = I(x,\Delta z)/\max_x I(x,0)$ |
| Modulation | $M = \dfrac{I_{clear}-I_{dark}}{I_{clear}+I_{dark}}$, with $I_{clear}=I_N(0)$, $I_{dark}=I_N(p/2)$ |
| Resist | positive resist clears where $D(x) = D_0 I_N(x) \ge D_{clear}$ |
| Target dose | $D_{target} = D_{clear}/I_N(p/4, 0)$ |

Assumptions: ideal binary amplitude mask, monochromatic coherent illumination, ideal hard pupil, surface-dose threshold resist. Lengths are in µm, dose in mJ cm⁻², and 2001 x-points are used across one pitch.

## Assigned case

| Case | λ (µm) | NA | D_clear (mJ cm⁻²) |
|---|---|---|---|
| A | 0.365 | 0.45 | 80 |
| B | 0.365 | 0.60 | 80 |
| **C (used here)** | **0.248** | **0.55** | **50** |
| D | 0.248 | 0.75 | 50 |

To run another case, change `CASE = "C"` in the setup cell.

## What the notebook covers

| Task | Content |
|---|---|
| 1 | Resolution and DOF, pitches 0.8 / 1.2 / 3.5 λ/NA, accepted orders, Fourier coefficients, predictions |
| 2 | Aerial image of the mask at the three pitches, order build-up, symmetry / non-negativity / zero-order checks |
| 3 | Best-focus modulation against 1/p (301 frequencies), first-order cutoff and Rayleigh comparison |
| 4 | Images at −DOF, 0, +DOF, M against focus, and the repeat with NA increased by 20 % |
| 5 | Target dose, printed CD at 0.8 / 1.0 / 1.2× dose, CD against dose at best focus and +0.75 DOF |
| 6 | Grid convergence (1001 / 2001 / 4001 points), limitations, partial coherence, verification ledger |

## Headline results (Case C)

| Quantity | Value |
|---|---|
| R = k₁λ/NA, DOF = k₂λ/NA² | 0.2705 µm, 0.4099 µm |
| m_max at 0.8 / 1.2 / 3.5 λ/NA | 0 / 1 / 3 |
| Best-focus M at those pitches | 0.0000 / 0.9715 / 0.9867 |
| M at −DOF / 0 / +DOF (p = 1.5 λ/NA) | 0.7442 / 0.9715 / 0.7442 |
| Same with NA × 1.2 (DOF = 0.2847 µm) | 0.8596 / 0.9715 / 0.8596 |
| D_target | 258.38 mJ cm⁻² |
| CD at 0.8 / 1.0 / 1.2× dose (target 0.3382 µm) | 0.3182 / 0.3382 / 0.3529 µm |
| Grid change, 2001 → 4001 points | −4.4×10⁻⁵ % |

At the working pitch the +20 % NA does not add a diffraction order (m_max stays 1), so best-focus M is unchanged. Only the DOF shrinks.

## Installation and usage

Requires Python 3.9+ with NumPy, Matplotlib and Jupyter. On Windows (Command Prompt), run each command separately:

```
py -3.12 -m pip install numpy matplotlib notebook
```

```
py -3.12 -m notebook
```

Open `Simulation_Assignment2.ipynb` and choose **Kernel → Restart & Run All**. Run the cells from top to bottom, since later cells use functions and variables defined in the setup cell.

## Outputs

Everything is written to `results/case_C/` on a clean run:

| File | Content |
|---|---|
| `fig1_order_capture.png` | Mask and best-focus image for the three pitches, plus order build-up |
| `task3_transfer_curve.png` / `.csv` | Modulation against 1/p |
| `task4_images.png`, `task4_focus_sweep.png` / `.csv` | Focus and NA comparison |
| `task5_dose_to_cd.png` / `.csv` | Dose-to-CD curves |
| `verification_ledger.csv` | All verification checks (19, all passing) |

## Verification

The notebook checks its own results: symmetry about x = 0, non-negative intensity, the m = 0-only limit, peak normalisation, vanishing even coefficients, Parseval's sum, Talbot self-imaging, focus symmetry, the closed form M = M₀ cos φ for m_max = 1, DOF scaling with NA, CD at the target dose, monotonic CD against dose, and grid convergence.

## Limitations

- **Partial coherence:** a single on-axis point source is assumed.
- **Resist physics:** one surface-dose threshold, with no absorption through depth, standing waves, bake diffusion or development rate.
- **Geometry:** one-dimensional periodic line-and-space only.
- **Ideal optics:** hard pupil, thin binary mask, scalar paraxial defocus (no aberrations, flare or polarisation).

## Repository layout

```
.
├── Simulation_Assignment2.ipynb   # simulator, plots and verification
├── README.md
└── results/
    └── case_C/                    # generated figures, CSV data, ledger
```

## Notes

This is university coursework. AI assistance was used and is disclosed in the project's contribution statement.
