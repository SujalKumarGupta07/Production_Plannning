# Fixed Costs with Binary Variables — Production Planning

This project solves Bindu Steel Works' 12-month production planning case study using Linear Programming (LP) and Mixed-Integer Linear Programming (MILP).

## Problem Overview

The company must satisfy monthly demand while minimizing total annual production and inventory costs. The model considers:

- Monthly variable production costs.
- Inventory holding cost of ₹30 per unit carried from one month to the next.
- Maximum monthly production capacity of 10,000 units.
- Zero initial inventory and zero inventory at the end of month 12.
- An optional fixed setup/rental cost for each month in which production occurs.

The problem is solved under three scenarios: fixed costs ignored, a fixed cost of ₹200,000 per production month, and a fixed cost of ₹400,000 per production month.

## Mathematical Model

### Decision Variables and Parameters

For each month $t = 1, 2, \ldots, 12$:

- $x_t$: Quantity of wire produced in month $t$.
- $I_t$: Inventory remaining at the end of month $t$.
- $d_t$: Demand for wire in month $t$.
- $c_t$: Variable production cost per unit in month $t$.
- $y_t$: Binary variable indicating whether production occurs in month $t$.
- $F$: Fixed setup/rental cost per production month.

### 1. Inventory Balance Constraints

The inventory balance ensures that monthly demand is satisfied using current production and available inventory.

$$
I_{t-1} + x_t = d_t + I_t
\qquad \forall t = 1,\ldots,12
$$

The initial and final inventory conditions are:

$$
I_0 = 0, \qquad I_{12} = 0
$$

### 2. Production Capacity and Binary Linking Constraints

Monthly production cannot exceed 10,000 units. The binary variable links production to the fixed setup cost.

$$
0 \leq x_t \leq 10{,}000y_t
\qquad \forall t = 1,\ldots,12
$$

$$
y_t \in \{0,1\}
\qquad \forall t = 1,\ldots,12
$$

When $y_t = 0$, production must be zero. When $y_t = 1$, production can range from 0 to 10,000 units.

Inventory must remain non-negative:

$$
I_t \geq 0
\qquad \forall t = 1,\ldots,12
$$

### 3. Objective Function Without Fixed Costs

When setup costs are ignored, the objective is to minimize variable production costs and inventory holding costs.

$$
\min Z =
\sum_{t=1}^{12} c_t x_t
+
30\sum_{t=1}^{11} I_t
$$

This formulation is a **Linear Programming (LP)** model because all decision variables are continuous and the objective and constraints are linear.

### 4. Objective Function With Fixed Costs

When setup costs are included, the objective becomes:

$$
\min Z =
\sum_{t=1}^{12} c_t x_t
+
30\sum_{t=1}^{11} I_t
+
F\sum_{t=1}^{12} y_t
$$

The fixed cost $F$ is incurred for every month in which production occurs. The binary variables represent this discrete decision.

This formulation is a **Mixed-Integer Linear Programming (MILP)** model.

## Model Parameters

| Parameter | Value |
|---|---:|
| Planning horizon | 12 months |
| Maximum monthly production | 10,000 units |
| Inventory holding cost | ₹30 per unit per month |
| Initial inventory | 0 units |
| Final inventory | 0 units |
| Setup cost — Scenario 1 | ₹0 |
| Setup cost — Scenario 2 | ₹200,000 per production month |
| Setup cost — Scenario 3 | ₹400,000 per production month |

## Optimization Scenarios

The notebook solves the model under the following scenarios:

1. **Without fixed costs:** Minimize variable production and inventory holding costs.
2. **Fixed cost of ₹200,000:** Include a setup/rental cost for every month with production.
3. **Fixed cost of ₹400,000:** Evaluate the effect of doubling the monthly setup/rental cost.

For each scenario, the notebook reports the optimal production schedule, ending inventory, cost breakdown, total objective value, and months in which production occurs.

## Project Files

- `fixed_costs_binary_variables_solution.ipynb` — Jupyter Notebook containing the mathematical formulation, optimization implementation, monthly production schedules, and cost comparisons.
- `requirements.txt` — Python dependencies required to run the notebook.

## Requirements

- Python 3.10 or later recommended.
- Jupyter Notebook, JupyterLab, or Google Colab.
- SciPy, NumPy, and pandas.

The notebook uses `scipy.optimize.milp` with the HiGHS optimization solver. No separate CBC installation is required.

## Installation and Usage

### 1. Download the Project Files

Keep the notebook and `requirements.txt` in the same directory.

### 2. Create and Activate a Virtual Environment (Recommended)

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

### 3. Install Dependencies

```bash
python -m pip install --upgrade pip
python -m pip install -r requirements.txt
```

### 4. Launch Jupyter Notebook

```bash
jupyter notebook
```

Open `fixed_costs_binary_variables_solution.ipynb` and select **Kernel → Restart Kernel and Run All Cells**. The menu wording may vary by Jupyter version.

Alternatively, upload the notebook to Google Colab and install the dependencies in a notebook cell:

```python
%pip install -r requirements.txt
```

If `requirements.txt` has not been uploaded to Colab, install the dependencies directly:

```python
%pip install "scipy>=1.11" "numpy>=1.24" "pandas>=2.0"
```

## Notebook Outputs

The notebook displays:

- The monthly variable production costs and demand data.
- Optimal monthly production quantities and ending inventory levels.
- Variable production costs, inventory holding costs, fixed costs, and total objective values.
- Months with and without production.
- A comparison of the three fixed-cost scenarios.

## Assumptions and Notes

- All monthly demand must be satisfied; shortages are not allowed.
- Initial inventory and final inventory are both zero.
- Production is limited to 10,000 units per month.
- Inventory holding cost is charged on inventory carried at the end of months 1 through 11. Month 12 incurs no holding cost because final inventory must be zero.
- When fixed costs are ignored, binary setup variables are unnecessary, and the problem is a linear program.
- When fixed costs are included, binary variables model whether production occurs in each month.
- The notebook uses `scipy.optimize.milp` with the HiGHS solver. Binary decisions are represented as integer variables with lower bound 0 and upper bound 1.
