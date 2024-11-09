# Pattern & Anomaly Detection

## Introduction

Pattern and anomaly detection is a technique used to identify patterns in data that do not conform to expected behavior. This technique is used in various fields such as fraud detection, network security, and predictive maintenance. Here, we will discuss the concepts of pattern and anomaly detection, as well as the methods used to implement them.

### SubSequence Matching

Subsequence matching is a technique used to identify similar patterns in time series data. It involves comparing subsequences of a time series to identify similar patterns. This technique is used in various fields such as speech recognition, gesture recognition, and financial forecasting.

`Subsequence`: A part or section of the full time series

`Matching`: Comparing subsequences of a time series to identify similar patterns

In order to compare subsequences, we need a distance measure. Once such distance measure is euclidean distance. The straight-line distance between two points in Euclidean space.

However Euclidean distance is not a good measure for time series data because it does not take into account the temporal order of the data points.

A much more precise and accurate distance measure for time series data is `Dynamic Time Warping (DTW)`. DTW is a distance measure used to compare two time series sequences. It is used to identify similar patterns in time series data.

```python
import numpy as np
from scipy.spatial.distance import euclidean
from fastdtw import fastdtw

# Generate two time series sequences
ts1 = np.array([1, 2, 3, 4, 5])
ts2 = np.array([1, 2, 3, 4, 5, 6, 7])

# Calculate the DTW distance between the two time series sequences
distance, path = fastdtw(ts1, ts2, dist=euclidean)
print(distance)
```

We must choose a subsequence length that is appropriate for the data and the patterns we are trying to identify. A longer subsequence length may capture more complex patterns, but it may also increase the computational complexity of the matching process.

A naive approach to subsequence matching is to compare each subsequence of the time series to the subsequence we are trying to match. However, this approach is computationally expensive and may not be practical for large time series data.

This approach typically uses the `Pythagorean theorem` to calculate the distance between two points in Euclidean space. However, as stated above, this approach does not take into account the temporal order of the data points, which is important in time series data.

This approach will also take into account a Vector of Distances (VOD) which is a vector that contains the distances between each point in the subsequence and the corresponding point in the time series.

This is also known as the `Distance Profile`.

The formula for this naive approach, where \( \mathbf{S} \) is the subsequence of length \( m \), is given by:

$$
d(i) = \sqrt{\sum_{j=1}^m (S_j - T_{i+j-1})^2}
$$

where:

- \( \mathbf{T} = \{ T_1, T_2, \ldots, T_n \} \) is the time-series data.
- \( \mathbf{S} = \{ S_1, S_2, \ldots, S_m \} \) is the subsequence.
- \( d(i) \) is the Euclidean distance between \( \mathbf{S} \) and the subsequence \( \mathbf{T}_{i:i+m-1} \).

Dynamic Time Warping (DTW) is a method for measuring similarity between two sequences that may vary in time or speed. The DTW distance between two sequences \( \mathbf{T} \) and \( \mathbf{S} \) is given by:

1. Initialize a cost matrix \( D \) of size \( (n+1) \times (m+1) \) where \( D(i, 0) = D(0, j) = \infty \) for all \( i \) and \( j \) except \( D(0, 0) = 0 \).

2. Populate the cost matrix using the following recurrence relation:

    \[ D(i, j) = \| T_i - S_j \| + \min(D(i-1, j), D(i, j-1), D(i-1, j-1)) \]

3. The DTW distance is the value in \( D(n, m) \).

Mathematically, the DTW distance can be expressed as:

$$
\begin{align*}
D(0, 0) &= 0 \\
D(i, 0) &= \infty \quad \text{for } i > 0 \\
D(0, j) &= \infty \quad \text{for } j > 0 \\
D(i, j) &= \| T_i - S_j \| + \min \left( D(i-1, j), D(i, j-1), D(i-1, j-1) \right) \quad \text{for } i, j > 0 \\
\text{DTW}(\mathbf{T}, \mathbf{S}) &= D(n, m)
\end{align*}
$$

where:

- \( \mathbf{T} = \{ T_1, T_2, \ldots, T_n \} \) is the time-series data.
- \( \mathbf{S} = \{ S_1, S_2, \ldots, S_m \} \) is the subsequence.
- \( D(i, j) \) is the cumulative cost matrix.

The DTW distance considers all possible alignments between the two sequences and finds the one with the minimum cumulative distance.

Whether we use Euclidean distance or DTW distance depends on the nature of the data and the patterns we are trying to identify. Euclidean distance is a simple and fast distance measure, but it may not capture the temporal order of the data points. DTW distance is a more complex distance measure that takes into account the temporal order of the data points, but it may be computationally expensive for large time series data.

We will then stack the distances into a `Pairwise Distance Matrix` which is a matrix that contains the `Distance Profiles`.

#### Time Complexity

The time complexity of the naive approach to subsequence matching is \( O(n^2 \cdot m) \), where \( n \) is the length of the time series and \( m \) is the length of the subsequence.

The time complexity of the DTW approach to subsequence matching is also \( O(n^2 \cdot m) \), where \( n \) is the length of the time series and \( m \) is the length of the subsequence.

These approaches compare each subsequence of the time series to the subsequence we are trying to match, resulting in a time complexity that is proportional to the product of the lengths of the time series and the subsequence.

Space complexity is \( O(n^2) \) for both approaches, where \( n \) is the length of the time series and \( m \) is the length of the subsequence.

This clearly shows that the space and time complexity of SubSequence Matching is very high and is not suitable for large datasets. These are not scalable approaches.

Ex: For a time series of

\( n = 20 times/min \cdot 60min/hr \cdot 24hrs/d \cdot 365 d/yr \cdot 5yr \)
\( n = 52,560,000 \) data points.

If we choose a subsequence length of \( m = 100 \), then the time complexity of the naive approach would be \( O(52,560,000^2 \cdot 100) \), which is not practical for large time series data.

That equates to \( O(2.76 \times 10^{17}) \) operations. Or `1,598.7 days` (4.4 years) and `11.1 PB` of memory.

Back of the Envelope Calculation:

\( (n^2-n)/2 \)

\(1,381,276,773,720,000 \cdot 0.0000001sec \)

`32bit`

#### Other Options

- STAMP
  - Uses FFT
  - Time complexity is \( O(n^2 \log n) \)
  - Space complexity is \( O(n) \)
- STOMP
  - Uses Algebra
  - Time complexity is \( O(n^2) \)
  - Space complexity is \( O(n) \)
- GPU-STOMP
  - Hardware - uses GPU
  - Time complexity is \( O(n^2) \)
  - Space complexity is \( O(n) \)
- CPU-Distributed-STOMP (STUMP/STUMPED)
  - Distributed Computing
  - Time complexity is \( O(n^2) \)
  - Space complexity is \( O(n) \)

A new library called `stumpy` has been developed that implements the STUMP algorithm. It is a highly efficient and scalable algorithm for subsequence matching that is suitable for large time series data.

Providing:

- Motif Discovery
- Anomaly Detection
- Time Series Chains
- Multi-Dimensional Time Series
- Segmentation
- MPdist Clustering
- Snippets
- Matrix Profile

```python

## Notes

- ARIMA (AutoRegressive Integrated Moving Average) is a popular method used for time series forecasting. It is a statistical model that uses past values to predict future values.
- `Dynamic Time Warping (DTW)` is a distance measure used to compare two time series sequences. It is used to identify similar patterns in time series data.
- Isolation Forest is an algorithm used for anomaly detection. It works by isolating anomalies in the data by randomly selecting a feature and then splitting the data based on a random value between the maximum and minimum values of the selected feature.
- Local Outlier Factor (LOF) is an algorithm used for anomaly detection. It calculates the local density of a data point relative to its neighbors and identifies outliers based on the deviation from the expected density.
- One-Class SVM (Support Vector Machine) is a machine learning algorithm used for anomaly detection. It learns the distribution of normal data points and identifies anomalies as data points that fall outside the learned distribution.
- K-Means clustering is a method used for pattern detection. It groups data points into clusters based on their similarity and identifies patterns in the data.
- DBSCAN (Density-Based Spatial Clustering of Applications with Noise) is a clustering algorithm used for pattern detection. It groups data points based on their density and identifies patterns in the data.
- Apriori algorithm is a data mining technique used for pattern detection. It is used to identify frequent item-sets in transaction data and generate association rules between items.
- Hidden Markov Models (HMMs) are probabilistic models used for pattern detection. They model sequences of observations and identify patterns in the data based on the transition probabilities between states.
- Long Short-Term Memory (LSTM) networks are a type of recurrent neural network used for pattern detection. They are capable of learning long-term dependencies in sequential data and identifying patterns in time series data.
- Self-Organizing Maps (SOMs) are a type of artificial neural network used for pattern detection. They map high-dimensional data onto a low-dimensional grid and identify patterns in the data based on the topology of the grid.
- Principal Component Analysis (PCA) is a dimensionality reduction technique used for pattern detection. It reduces the dimensionality of the data by projecting it onto a lower-dimensional space and identifies patterns in the data based on the variance of the data.
- t-SNE (t-Distributed Stochastic Neighbor Embedding) is a dimensionality reduction technique used for pattern detection. It maps high-dimensional data onto a lower-dimensional space and identifies patterns in the data based on the similarity of data points.
- Frequent Pattern Mining is a data mining technique used for pattern detection. It is used to identify frequent patterns in transaction data and generate association rules between items.
- Sequential Pattern Mining is a data mining technique used for pattern detection. It is used to identify sequential patterns in transaction data and generate association rules between items.

## References
