# 16-qubit neutron-scattering tutorial: Windows setup (Python 3.13)

Runs entirely on your PC. No IBM account, API key or QPU time needed.
Full explanations are in `Neutron_Scattering_16q_Tutorial.pdf` (chapters 3, 4 and 9).

## 1. Python 3.13

```powershell
py -3.13 --version
```

If `py` is "not recognized", either call Python by its full path
(`& "$env:LOCALAPPDATA\Programs\Python\Python313\python.exe"` in place of `py -3.13`)
or install it with `winget install Python.Python.3.13` and reopen PowerShell.

## 2. Virtual environment

Put `requirements.txt` and the notebooks in one folder (for example `C:\python3_13\quantum_test`), open PowerShell there:

```powershell
py -3.13 -m venv .venv
.venv\Scripts\Activate.ps1
```

If activation is blocked: `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned`, then activate again.

## 3. Packages

```powershell
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
python -c "import qiskit, quimb, jax, qiskit_addon_aqc_tensor, qiskit_quimb; print('OK, qiskit', qiskit.__version__)"
```

## 4. Kernel and notebook

```powershell
python -m ipykernel install --user --name neutron-313 --display-name "Python 3.13 (neutron)"
jupyter lab
```

Open `Quantum_Code_16q_tutorial.ipynb`, choose the **Python 3.13 (neutron)** kernel
(VS Code: Select Kernel > Python Environments > the `.venv` one), and run all cells top to bottom.
Steps 2 (AQC compression) and 3 (simulation) are the slow parts, a few minutes each; Step 3 prints nothing until it finishes.

## 5. If the kernel crashes ("unknown" / "dead")

It ran out of memory and all variables are lost.

1. Restart / reconnect the kernel, close other programs, and re-run from the top.
2. Still crashing: set `n_qubits = 12` (keep it even), or `aqc_n_steps_2 = 0`.
3. `MemoryError` in Step 2 means the same thing: shrink `n_qubits` first; keep `mps_max_bond = 32`.
4. `AccountNotFoundError` means code is trying to reach IBM hardware; use the local Step 3 (`StatevectorEstimator`).

## Settings used (vs IBM's large-scale run)

| Setting | IBM | This tutorial |
|---|---|---|
| n_qubits | 50 | 16 |
| aqc_n_steps_1 + aqc_n_steps_2 | 6 + 4 | 3 + 2 |
| mps_max_bond | 128 | 32 |
| n_steps, time_step | 20, 0.6 | 20, 0.6 |
| gs_n_layers | 5 | 5 |
| Step 3 | IBM Heron QPU + EstimatorV2 | Local StatevectorEstimator |
