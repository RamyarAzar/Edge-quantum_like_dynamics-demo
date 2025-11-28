# Modal decomposition and emergent quantum-like dynamics in a geometric field

**Author:** Ramyar  
**Last updated:** November 26, 2025  

This repository contains a working implementation of **spectral / modal decomposition** for a geometry-generated scalar field \(w(x,t)\), together with diagnostics that reveal **quantum-like behavior** in the resulting mode amplitudes and energies.

The code here is the same core used in the corresponding case study on my modeling website and serves both as a **demonstration of methods** and as a **reproducible numerical experiment**.

---

## Snapshot

### Field
- Mathematical physics  
- Spectral modeling & emergent quantum-like dynamics  

### Model type
- Spectral / modal decomposition of a geometry-derived field  
- Time-dependent coefficients \(c_k(t)\) and effective Hamiltonian \(H_\text{eff}(t)\)

### Stage
- Advanced prototype  
- Full simulation and post-processing pipeline implemented on a  
  \(400 \times 400 \times 400 \times 4 \times 4\) grid over 50 time steps  

### Main question
If the scalar field \(w(x,t)\) is generated purely from hyperspherical geometry, can we decompose it into a set of orthonormal modes and time-dependent amplitudes that behave like a **quantum spectrum**?

More concretely:

> When we write
> \[
>   w(x,t) = \sum_k c_k(t)\,\psi_k(x)
> \]
> on the full 400³ grid, do the modal energies \(|c_k(t)|^2\) define a nearly conserved effective Hamiltonian, and do hybrid combinations of modes show a sharp energy drop and stabilization around \(t \approx 36\), analogous to equilibration in quantum systems?

---

## Model & approach

Starting from the scalar field \(w(x,t)\) that emerges from gradients of the radial function \(R(\chi,\theta,t)\) on the hypersphere, we expand \(w\) in a **modal basis**:
\[
  w(x,t) = \sum_k c_k(t)\,\psi_k(x).
\]

- The modes \(\psi_k(x)\) are obtained via **spin-weighted spherical harmonics** and related spectral transforms on each time-slice \(\Sigma_t\) of the hypersphere.
- These modes form an **orthonormal basis** on each slice, so the coefficients \(c_k(t)\) act as generalized “occupation amplitudes” of geometric excitations.

On the numerical side:

- The field is stored as memory-mapped arrays on a grid  
  \(400_\chi \times 400_\theta \times 400_\phi \times 4 \times 4\) (tensor indices) \(\times 50\) time steps.
- Each time slice is passed through a **harmonic / spectral pipeline**, followed by **SVD/PCA filtering** to isolate dominant modes.
- In practice, fewer than \(\sim 500\) modes reconstruct most of the dynamics, defining a compact effective “Hilbert space”:
  - low-\(k\) modes ≈ global breathing and large-scale deformations,
  - high-\(k\) modes ≈ localized modons and vortices.

Time series of the coefficients \(c_k(t)\) show three qualitative behaviors:

1. Stable low-\(k\) modes with regular oscillations,  
2. Transient high-\(k\) bursts that grow and decay quickly,  
3. Beating / interference patterns in intermediate modes.

Defining modal energies
\[
  E_k(t) = |c_k(t)|^2, \qquad
  H_\text{eff}(t) = \sum_k |c_k(t)|^2,
\]
we obtain an **emergent Hamiltonian** that is approximately conserved; deviations reflect internal energy redistribution rather than numerical instability.

To probe **quantum-like superposition**, we construct hybrid modes
\[
  \psi_\pm = \psi_i \pm \psi_j
\]
and track
\[
  E_\pm(t) = \int \big|\psi_i(x,t) \pm \psi_j(x,t)\big|^2 \, d^4x.
\]

A broad family of such hybrid pairs exhibits a **sharp energy drop around \(t \approx 35{-}36\)** followed by stabilization at lower plateaus, interpreted as rapid reorganization and equilibration of modal interference.

In parallel, the same modal structure is used to define an **effective Schrödinger-like dynamics**. Introducing emergent constants \(\lambda(t)\), \(\tau(t)\), and
\[
  \hbar_\text{eff}(t) = \lambda^2(t)\,\tau(t),
\]
we recover an evolution of the form
\[
  i\,\hbar_\text{eff}\,\partial_t \psi_k \;\approx\; \hat{H}^{(k)} \psi_k,
\]
where \(\hat{H}^{(k)}\) is an effective Hamiltonian built from geometric Laplacians and a numerically extracted potential. This closes the loop between pure geometry and **quantum-like modal behavior**.

---

## Repository contents

> Adjust filenames and directory names to match your actual implementation.

```text
README.md                     # This file

src/
  load_field.py               # Loads w(x,t) from geometric simulation outputs
  spectral_basis.py           # Builds ψ_k(x) via spin-weighted harmonics
  modal_decomposition.py      # Computes c_k(t) and E_k(t) from w(x,t)
  svd_pca_filtering.py        # Dimensionality reduction and mode selection
  hybrid_modes.py             # Constructs ψ_± and hybrid energies E_±(t)
  emergent_hamiltonian.py     # Computes H_eff(t) and related diagnostics
  run_full_spectral_pipeline.py  # Orchestrates full modal analysis

config/
  spectral_params.yaml        # Mode cutoffs, PCA thresholds, time windows, etc.

data/
  input_field/                # Geometry-derived w(x,t) (or pointers to it)
  mode_decomposition_output/  # Spatial modes ψ_k(x)
  spectral_analysis_outputs/  # Time series of c_k(t), E_k(t), H_eff(t)
  spectral_alm_output/        # Harmonic coefficients on the hypersphere
  wdiff_spectral_output/      # Spectra of derivatives / fluctuations
  quantum_clusters/           # Mode clustering and dominant excitations
  quantum_peaks/              # Peak detection in spectra
  quantum_outputs/            # Hybrid-mode energies, energy-drop curves

plots/
  spectra_overview.png        # Example energy spectra
  heff_time_series.png        # H_eff(t) over 50 time steps
  hybrid_energy_drop.png      # Energy drop around t ≈ 36

```
## How to run

> The full-resolution analysis (400³ grid × 4 × 4 tensors × 50 time steps) is computationally heavy.  
> For testing and exploration, reduce grid size and/or the number of modes in `config/spectral_params.yaml`.

### 1. Set up environment

Create and activate a Python environment (e.g., `venv` or `conda`), then install dependencies:

```bash
pip install -r requirements.txt
```
### 2. (Optional) Adjust parameters

Edit `config/spectral_params.yaml` to change, for example:

- grid / field resolution (for test runs),
- maximum harmonic index and number of retained modes,
- PCA / SVD thresholds,
- time window for analysis.

### 3. Run the spectral pipeline

```bash
python src/run_full_spectral_pipeline.py
```
Results will be written to `data/` and diagnostic plots to `plots/`.

---

## Relation to the EDGE framework

This spectral engine sits on top of the geometric core of the **EDGE** model:

- It takes the geometry-derived scalar field \(w(x,t)\) as input,  
- extracts **modes, energies, and hybrid superpositions**,  
- and reveals **quantum-like equilibration** and **effective Hamiltonian** behavior.

In the broader EDGE program, this module provides the bridge between:

- pure geometry (hyperspherical spacetime and fields), and  
- quantum-like dynamics formulated in terms of **emergent modes and spectra**.

For additional context and other case studies, see the corresponding pages on the modeling website.

---

## Code archive & publications

All modal decomposition and spectral-analysis assets  
(`mode_decomposition_output`, `spectral_analysis_outputs`, `spectral_alm_output`,  
`wdiff_spectral_output`, `quantum_clusters`, `quantum_peaks`, `quantum_outputs`)  
are included in the archived EDGE simulation package:

- **Simulation & code archive (Zenodo):** DOI `10.5281/zenodo.15873778`  
  (restricted license; reuse by agreement with the author)

The main EDGE manuscript, which includes:

- the modal decomposition,  
- the hybrid-mode energy-drop figure, and  
- the emergent Hamiltonian analysis,

is currently being prepared for journal publication.

Full code and a more detailed technical report can be shared privately with collaborators or reviewers under NDA or a specific collaboration agreement.

---

## License & usage

This repository is provided as a **demonstration and case study**.

- Use is limited to **personal, academic, and non-commercial** purposes.
- Do **not** integrate this code into clinical, diagnostic, or commercial products.
- Redistribution of modified or unmodified versions is **not permitted** without explicit written permission from the author.

For collaboration, extended access, or use within a larger project, please contact the author.
