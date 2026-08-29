# Integrated Demand Forecasting & Capacitated Multi-Echelon Inventory Allocation

An end-to-end Operations Research & Machine Learning decision pipeline connecting predictive time-series regression with prescriptive Mixed-Integer Linear Programming (MILP) to optimize retail inventory fulfillment under supply network constraints.

---

## 1. Project Overview
* **Predictive Stage:** Regularized Ridge regression trained on strictly leakage-free lag features ($t-1, t-7, t-14$) and 7-day rolling window statistics.
* **Prescriptive Stage:** Capacitated Multi-Echelon Mixed-Integer Linear Program (MILP) formulated in `PuLP` to optimize stock replenishment across regional fulfillment centers.
* **Impact:** Reduces cumulative logistics expenditure by **13.7%** compared to an industry moving-average baseline, while elevating customer order fulfillment to a **98.0% SLA**.

---

## 2. Benchmark Comparison & Key Metrics

| Metric | Moving-Average Baseline | Optimized MILP Pipeline | Delta / Improvement |
| :--- | :---: | :---: | :---: |
| **Total Logistics Cost** | $112,179.50 | $96,866.00 | **-13.7% Savings** |
| **Fulfillment SLA** | 96.45% | **97.97% (~98.0%)** | **+1.5% Gain** |
| **Forecast Accuracy** | — | **13.56% WAPE (5.63 MAE)** | — |

---

## 3. Mathematical Optimization Formulation

### Objective Function
$$\min_{\mathbf{x}, \mathbf{U}} \quad \sum_{w \in W}\sum_{s \in S}\sum_{i \in I} c_{w,s} \cdot x_{w,s,i} + \sum_{s \in S}\sum_{i \in I} \pi \cdot U_{s,i}$$

Where:
* $x_{w,s,i} \in \mathbb{Z}_{\ge 0}$: Discrete units of item $i$ dispatched from warehouse $w$ to store $s$.
* $U_{s,i} \ge 0$: Auxiliary variable tracking unmet customer demand for item $i$ at store $s$.
* $c_{w,s}$: Unit freight transportation cost from warehouse $w$ to store $s$.
* $\pi$: Shortage penalty parameter per unit of unmet customer demand ($30.00/\text{unit}$).

### Operational Constraints
1. **Warehouse Inbound Capacity Limitations:**
   $$\sum_{s \in S} x_{w,s,i} \le C_{w,i} \quad \forall w \in W, \; \forall i \in I$$
2. **Demand Fulfillment & Stockout Balance:**
   $$\sum_{w \in W} x_{w,s,i} + U_{s,i} \ge \hat{d}_{s,i} \quad \forall s \in S, \; \forall i \in I$$
3. **Store Inventory Ceiling:**
   $$\sum_{w \in W} x_{w,s,i} \le 1.10 \cdot \hat{d}_{s,i} \quad \forall s \in S, \; \forall i \in I$$

---

## 4. Repository Structure
