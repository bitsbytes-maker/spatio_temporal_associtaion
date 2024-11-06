# Spatio-Temporal Association: Anomalous Windows and Influence Score


## 3.1 Anomalous Windows

An anomalous window refers to a group of spatial objects that are spatially close and share similar spatial and non-spatial attributes, demonstrating unusual behavior when compared to the rest of the data. For example:

- **Child Poverty Dataset**: This dataset tracks the number of children living in poverty in various regions. An anomalous window would represent an area with unusually high child poverty rates relative to nearby areas.
  
- **Unemployment Dataset**: Similarly, an anomalous window in this dataset represents regions with unusually high unemployment rates.

Although these two phenomena (child poverty and unemployment) measure different aspects, they may overlap or be close, revealing interesting spatial relationships between them.

## 3.2 Influence Distance and Influence Score

### Influence Distance

The **influence distance** quantifies the relationship between two spatial objects (locations), considering both geographic proximity and other related factors. For example, if two regions exhibit clusters of child poverty, the influence distance measures how far apart they are geographically, factoring in local characteristics like socioeconomic conditions that influence poverty. 

#### Formula:
\[
d_{v_{p→q}} = \text{number of hops from } p \text{ to } q
\]
Where:
- `d_{v_{p→q}}` is the influence distance between location `p` and location `q`.
- The influence distance is the number of hops (steps through neighboring regions) it takes to travel from `p` to `q`.

#### Example:
- If **Location 1 (L1)** and **Location 4 (L4)** are connected via two hops (through two intermediate locations), the influence distance `d_{child poverty1→4}` would be:
\[
d_{child poverty1→4} = 2
\]

### Influence Score

The **influence score** quantifies the influence one spatial object has on another, based on the influence distance. The closer the two regions are geographically and in terms of the phenomena being measured, the higher the influence score.

#### Formula:
\[
s_{v_{p→q}} = e^{-(d_{v_{p→q}} \times (1/\delta_v))}
\]
Where:
- `d_{v_{p→q}}` is the influence distance between regions `p` and `q`.
- `δ_v` is the decay rate, modeling how influence diminishes with distance.

#### Example Calculations:
1. **Child Poverty Influence Score (L1 → L4)**:
   - Influence distance: 2
   - Decay rate (`δ`) = 10
   - Calculation:
     \[
     s_{v_{1→4}} = e^{-(2 \times (1/10))} = e^{-0.2} ≈ 0.8187
     \]

2. **Unemployment Influence Score (L1 → L3)**:
   - Influence distance: 1
   - Decay rate (`δ`) = 50
   - Calculation:
     \[
     s_{v_{1→3}} = e^{-(1 \times (1/50))} = e^{-0.02} ≈ 0.9802
     \]

## 3.3 Calculating Influence Between Anomalous Windows

When comparing two anomalous windows, say **Child Poverty** and **Unemployment**, the influence score between these windows is calculated by finding the influence score between each spatial object within the windows.

### Example:
- **Child Poverty Window** `Av = {L1, L2, L3, L4}` and **Unemployment Window** `Av' = {L3, L4, L5, L6}`.
- For each pair of regions between the two windows, calculate the influence score and take the maximum score.

#### Influence Scores:
- **Influence Score Between L1 (Child Poverty) and L3 (Unemployment)**: 
  \[
  s_{v_{1→3}} = 0.8
  \]
- **Influence Score Between L3 (Child Poverty) and L5 (Unemployment)**: 
  \[
  s_{v_{3→5}} = 0.9
  \]
- **Influence Score Between L4 (Child Poverty) and L6 (Unemployment)**: 
  \[
  s_{v_{4→6}} = 0.7
  \]

### Final Influence Score Calculation

To calculate the overall influence score between the two anomalous windows, we use the following formula:
\[
s_{Av→Av'} = \frac{1}{|Av'|} \sum_{q \in Av'} \max_{p \in Av} (s_{v_{p→q}})
\]
Where:
- `Av` and `Av'` represent the anomalous windows (Child Poverty and Unemployment, respectively).
- `|Av'|` is the number of regions in the Unemployment window (`Av'`), which is 4 in this case.
- We compute the maximum influence score between each region in `Av` (Child Poverty) and `Av'` (Unemployment).

#### Step-by-Step Calculation:
1. **Maximum influence score between L1 (Child Poverty) and all regions in Av'**:
   - Max(0.8, 0.7) = 0.8
2. **Maximum influence score between L2 (Child Poverty) and all regions in Av'**:
   - Max(0.9, 0.7) = 0.9
3. **Maximum influence score between L3 (Child Poverty) and all regions in Av'**:
   - Max(0.9, 0.7) = 0.9
4. **Maximum influence score between L4 (Child Poverty) and all regions in Av'**:
   - Max(0.7, 0.7) = 0.7

#### Final Calculation:
\[
s_{Av→Av'} = \frac{1}{4} \left( 0.8 + 0.9 + 0.9 + 0.7 \right) = \frac{3.3}{4} = 0.825
\]

Thus, the final influence score between the **Child Poverty** and **Unemployment** anomalous windows is **0.825**.

## Conclusion

This process enables you to quantify spatial relationships between different phenomena (e.g., Child Poverty and Unemployment) by calculating the influence score between their anomalous windows. The influence score helps identify regions where the phenomena are likely to be correlated, providing valuable insights for spatial data analysis and decision-making.


# Pairwise Influence Relationship Approach - Algorithm Breakdown

## Overview
This document describes a step-by-step explanation of the **Pairwise Influence Relationship Approach** used to analyze anomalous windows across multiple domains over time. The algorithm computes influence scores and relationships between two domains (e.g., child poverty and unemployment) using a set of time intervals and location-based data.

## Inputs:
- **Anomalous windows**: These represent sets of locations identified as anomalous for different domains (e.g., child poverty and unemployment).
- **Time intervals**: The anomalous windows are provided for different time slices (e.g., T1, T2, T3, etc.).
- **Influence scores**: These quantify the influence of one anomalous window on another.

## Example values:

### Time intervals and anomalous windows:

| **Time Interval (T)** | **Child Poverty (CP)** | **Unemployment (U)** |
|-----------------------|------------------------|----------------------|
| T1                    | {L1, L2, L3}           | {L2, L3, L4, L5}     |
| T2                    | {L3, L4, L5, L8, L10}  | {L2, L6, L7, L8}     |
| T3                    | {L6, L7, L3, L2}       | {L1, L6, L8, L9}     |
| T4                    | {L3, L5, L4, L7, L2}   | {L4, L6, L9, L2, L3} |

### Decay rates (Example values):
- T1: 10
- T2: 10
- T3: 10
- T4: 10

### Influence scores (Example values):

| **Time Interval (T)** | **Influence Score** |
|-----------------------|---------------------|
| T1                    | 0.72620             |
| T2                    | 0.90710             |
| T3                    | 0.70241             |
| T4                    | 0.76193             |

---

## Algorithm Steps

### Step 1: Compute Influence Distance for Each Pair of Locations Between the Two Anomalous Windows at Time `Ti`
We first calculate the **influence distance** (`dSvm_i → Svn_i`) for each pair of locations from the **Child Poverty (CP)** and **Unemployment (U)** domains.

#### Example for Time Interval T1:

- **Child Poverty (CP)**: {L1, L2, L3}
- **Unemployment (U)**: {L2, L3, L4, L5}

Compute the influence distance for each pair:
- (L1, L2), (L1, L3), (L1, L4), (L1, L5)
- (L2, L2), (L2, L3), (L2, L4), (L2, L5)
- (L3, L2), (L3, L3), (L3, L4), (L3, L5)

The **influence distance** calculation could be based on metrics like geographical proximity or economic factors, which would vary for each pair.

---

### Step 2-6: Compute the Maximum Influence Score for Each Location in CP with Each Location in U

For each pair, we compute the **maximum influence score** (`max{dSvm_i → Svn_i}`) based on previously calculated distances and scores.

#### Example for Time Interval T1:
- Compute maximum influence scores for each CP location with each U location:

| **Location (CP)** | **Best Matching Location (U)** | **Influence Score** |
|-------------------|---------------------------------|---------------------|
| L1                | L2                              | 0.72620             |
| L1                | L3                              | 0.5                 |
| L2                | L2                              | 0.9                 |
| L2                | L3                              | 0.75                |
| L3                | L2                              | 0.8                 |
| L3                | L3                              | 0.85                |

---

### Step 7: Compute the Overall Influence Score for CP on U at Time `Ti`

Next, calculate the **overall influence score** for CP on U by averaging the maximum influence scores calculated in the previous step.

#### Example for Time Interval T1:
The maximum influence scores for CP and U are:

- L1 → L2: 0.72620
- L2 → L2: 0.9
- L3 → L2: 0.8

The **overall influence score** can be calculated as:

\[
\text{Overall Influence Score} = \frac{0.72620 + 0.9 + 0.8}{3} = 0.80873
\]

---

### Step 8-12: Compute the Best Pairing of Locations Between CP and U

This step involves determining the **best pairing** between locations in CP and U based on the influence scores.

#### Example for Time Interval T1:

- **Best match for L1**: L2 (with an influence score of 0.72620)
- **Best match for L2**: L2 (with an influence score of 0.9)
- **Best match for L3**: L2 (with an influence score of 0.8)

---

### Step 13-15: Quantify the Associations Using Confidence, Support, and Lift

Finally, we quantify the **association** between CP and U using metrics like **confidence**, **support**, and **lift**.

#### 1. **Confidence**: 
Confidence measures the likelihood that an association between CP and U is true. It is calculated as:

\[
\text{Confidence}(A → B) = \frac{\text{Number of time intervals where both CP and U are anomalous}}{\text{Total number of time intervals}}
\]

**Example**:
Let's say we are calculating the confidence for **L1 → L2**. We have the following data:

- **T1**: Both CP (L1) and U (L2) are anomalous.
- **T2**: CP (L3) and U (L2) are anomalous.
- **T3**: CP (L6) and U (L1) are anomalous.
- **T4**: CP (L3) and U (L2) are anomalous.

If **L1 → L2** is anomalous in 2 time intervals (T1 and T2), and there are a total of 4 time intervals (T1 to T4), then the confidence is:

\[
\text{Confidence}(L1 → L2) = \frac{2}{4} = 0.5
\]

#### 2. **Support**:
Support measures how often an association occurs across all time intervals. It is calculated as:

\[
\text{Support}(A → B) = \frac{\text{Number of time intervals where both CP and U are anomalous}}{\text{Total number of time intervals}}
\]

**Example**:
Using the same data as in confidence calculation, if **L1 → L2** appears in 2 out of 4 time intervals, then:

\[
\text{Support}(L1 → L2) = \frac{2}{4} = 0.5
\]

#### 3. **Lift**:
Lift measures how much more likely the association is compared to if the two events were independent. It is calculated as:

\[
\text{Lift}(A → B) = \frac{\text{Confidence}(A → B)}{\text{Support}(A) \times \text{Support}(B)}
\]

**Example**:
If the support for **L1** in CP is 0.75 and the support for **L2** in U is 0.5, the lift for **L1 → L2** is:

\[
\text{Lift}(L1 → L2) = \frac{0.5}{0.75 \times 0.5} = 1.3333
\]

This suggests that **L1** (Child Poverty) and **L2** (Unemployment) are more likely to occur together than expected by chance.

---

## Example Summary for Time Interval T1:

1. **Child Poverty (CP)**: {L1, L2, L3}
2. **Unemployment (U)**: {L2, L3, L4, L5}

3. **Compute Influence Distance** between each pair of locations.
4. **Maximum Influence Score** for each location:
   - L1 → L2: 0.72620
   - L2 → L2: 0.9
   - L3 → L2: 0.8

5. **Overall Influence Score**: 0.80873 for T1.

6. **Best Pairing** between CP and U:
   - L1 → L2, L2 → L2, L3 → L2.

7. **Confidence**: 0.5 (for L1 → L2).
8. **Support**: 0.5 (for L1 → L2).
9. **Lift**: 1.3333 (for L1 → L2).

---

## Conclusion

This algorithm helps in understanding and quantifying the relationships between different domains (e.g., child poverty and unemployment) and their anomalous windows over time, providing valuable insights into the influence dynamics between these domains.

---
