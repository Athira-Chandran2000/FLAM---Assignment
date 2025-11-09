# Assignment

I used a data fitting approach using the provided points on the curve for solving the problem for finding the unknown variables θ, M, and X, 


1. Used Principal Component Analysis (PCA) to find the principal direction of the points, which gives an initial estimate for θ (in radians) as the angle of the main direction.
2. Calculated the initial X using the mean of the points and the estimated mean t = (mean_y - 42) / sin(θ).
3. Defined a residual function that, for given θ, M, X, computes for each point the projected t and the perpendicular deviation d, and the predicted d from the formula.
4. Used least squares optimization to minimize the sum of squared residuals to find the best fit θ, M, X.

$$
\left(t*\cos(0.524)-e^{0.030\left|t\right|}\cdot\sin(0.3t)\sin(0.524)\ +55 ,42 + t*\sin(0.524)+e^{0.030\left|t\right|}\cdot\sin(0.3t)\cos(0.524)\right)
$$


