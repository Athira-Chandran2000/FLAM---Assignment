# Assignment

I used a data fitting approach using the provided points on the curve for solving the problem for finding the unknown variables θ, M, and X, 


1. Used Principal Component Analysis (PCA) to find the principal direction of the points, which gives an initial estimate for θ (in radians) as the angle of the main direction.
2. Calculated the initial X using the mean of the points and the estimated mean t = (mean_y - 42) / sin(θ).
3. Defined a residual function that, for given θ, M, X, computes for each point the projected t and the perpendicular deviation d, and the predicted d from the formula.
4. Used least squares optimization to minimize the sum of squared residuals to find the best fit θ, M, X.

$$
\left(t*\cos(0.524)-e^{0.030\left|t\right|}\cdot\sin(0.3t)\sin(0.524)\ +55 ,42 + t*\sin(0.524)+e^{0.030\left|t\right|}\cdot\sin(0.3t)\cos(0.524)\right)
$$


## Code
import numpy as np
import pandas as pd
from scipy.optimize import least_squares

### Load data
df = pd.read_csv('xy_data.csv')

points = df[['x', 'y']].values

def compute_residuals(params, points):
    theta, M, X = params
    cos_th = np.cos(theta)
    sin_th = np.sin(theta)
    v = np.array([cos_th, sin_th])
    w = np.array([-sin_th, cos_th])
    p0 = np.array([X, 42])
    residuals = []
    for p in points:
        vec = p - p0
        t = np.dot(vec, v)
        if t < 6 or t > 60:
            residuals.append(100)
            continue
        d = np.dot(vec, w)
        pred_d = np.exp(M * abs(t)) * np.sin(0.3 * t)
        residuals.append(d - pred_d)
    return residuals

### Initial guess using PCA

mean = np.mean(points, axis=0)

centered = points - mean

cov = np.cov(centered.T)

eigvals, eigvecs = np.linalg.eig(cov)

idx = np.argmax(eigvals)
direction = eigvecs[:, idx]
theta_init = np.arctan2(direction[1], direction[0])
sin_th = np.sin(theta_init) or 1e-6
mean_t = (mean[1] - 42) / sin_th
cos_th = np.cos(theta_init)
X_init = mean[0] - mean_t * cos_th
params_init = [theta_init, 0.0, X_init]

### Optimize
res = least_squares(compute_residuals, params_init, args=(points,))
print(res.x)
