# Fixed Costs with Binary Variables — Production Planning

This project solves Bindu Steel Works' 12-month production planning case study using linear and mixed-integer linear optimization.

## Problem overview

The company must meet monthly demand while minimizing total annual cost. The model includes:

- Monthly variable production costs.
- Inventory holding cost of ₹30 per unit carried from one month to the next.
- Monthly production capacity of 10,000 units.
- Zero initial inventory and zero inventory at the end of month 12.
- An optional fixed setup/rental cost for every month in which production occurs.

## Mathematical model

For month \(t = 1, \ldots, 12\):

- \(x_t\): production quantity in month \(t\).
- \(I_t\): inventory at the end of month \(t\).
- \(d_t\): demand in month \(t\).
- \(c_t\): variable production cost per unit.
- \(y_t\): binary variable, equal to 1 when the facility is used for production and 0 otherwise.
- \(F\): fixed cost per production month.

### Inventory balance

\[
I_{t-1} + x_t = d_t + I_t
\]

with \(I_0 = 0\) and \(I_{12} = 0\).

### Production capacity and binary linking

\[
0 \leq x_t \leq 10000 y_t,\qquad y_t \in \{0,1\}
\]

When fixed costs are ignored, the binary variables are unnecessary and the model is a linear program.

### Objective without fixed costs

\[
\min \sum_{t=1}^{12} c_t x_t + 30\sum_{t=1}^{11} I_t
\]

### Objective with fixed costs

\[
\min \sum_{t=1}^{12} c_t x_t + 30\sum_{t=1}^{11} I_t + F\sum_{t=1}^{12} y_t
\]

The notebook solves the model for:
1. Fixed costs ignored.
2. Fixed cost of ₹200,000 per production month.
3. Fixed cost of ₹400,000 per production month.

## Files

- `fixed_costs_binary_variables_solution.ipynb` — complete notebook with the formulation, solver implementation, monthly schedules, and cost comparisons.
- `requirements.txt` — Python dependencies.

## Requirements

- Python 3.10 or later recommended.
- Jupyter Notebook, JupyterLab, or Google Colab.
- SciPy (includes the HiGHS optimization solver), NumPy, and pandas.

No separate CBC installation is required.

## Installation and usage

### 1. Download the files

Keep the notebook and `requirements.txt` in the same directory.

### 2. Create and activate a virtual environment (optional but recommended)

**macOS / Linux**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

**Windows PowerShell**

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Launch Jupyter

```bash
jupyter notebook
```

Open `fixed_costs_binary_variables_solution.ipynb` and choose **Kernel → Restart Kernel and Run All Cells** (menu wording may vary).

Alternatively, upload the notebook to Google Colab and install the requirements in a notebook cell:

```python
%pip install -r requirements.txt
```

If running in Colab without uploading `requirements.txt`, use:

```python
%pip install "scipy>=1.11" "numpy>=1.24" "pandas>=2.0"
```

## Notebook outputs

The notebook displays:

- Input data table (monthly variable cost and demand).
- Optimal monthly production and ending inventory.
- Variable production cost, inventory holding cost, fixed cost, and total objective value.
- Months with and without production.
- A comparison of the three cost scenarios.

## Notes

- Holding cost is charged on inventory carried into the next month. Month 12 has no holding cost because the required ending inventory is zero.
- All demand must be met; shortages are not allowed.
- The notebook uses `scipy.optimize.milp` with the HiGHS solver. Binary decisions are represented by integer variables with lower bound 0 and upper bound 1.
