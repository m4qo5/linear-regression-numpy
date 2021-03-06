# How to implement Linear Regression in Python from scratch?
Example of the Linear Regression class, written from scratch.

This is a training example which could help understand more how linear regression works.

## Code

```python
class LinearRegression:
    def __init__(self, learing_rate=0.01, n_iter=10000):
        self.learing_rate = learing_rate  # learning rate alpha hyperparemeter
        self.n_iter = n_iter  # number of iterations to minimize the cost function

    def train(self, X, y):
        self._n_samples = len(y)  # size of the dataset (rows), in formulas m.
        self.X = np.hstack((np.ones(
            (self._n_samples, 1)), (X - np.mean(X, 0)) / np.std(X, 0)))  # scaled features, added "zero" feature vector with all values = 1
        self.n_features = np.size(X, 1)  # total number of the features
        self.y = y[:, np.newaxis]  # creating a target variable vector
        self.params = np.zeros((self.n_features + 1, 1))  # setting weights to zeroes as start values

        for _ in range(self.n_iter):  # using Gradient descent, simultaneously updating weights
            self.params = self.params - (self.learing_rate/self._n_samples) * \  
            self.X.T @ (self.X @ self.params - self.y)

        self.intercept_ = self.params[0]  # bias
        self.weights = self.params[1:]  # our weights for features

    def score(self, X=None, y=None):
        if X is None:
            X = self.X
        else:
            n_samples = np.size(X, 0)
            X = np.hstack((np.ones(
                (n_samples, 1)), (X - np.mean(X, 0)) / np.std(X, 0)))
        if y is None:
            y = self.y
        else:
            y = y[:, np.newaxis]
        y_pred = X @ self.params
        score = 1 - (((y - y_pred)**2).sum() / ((y - y.mean())**2).sum())  # Coefficient of Determination
        return score

    def predict(self, X):
        n_samples = np.size(X, 0)
        y = np.hstack((np.ones((n_samples, 1)), (X-np.mean(X, 0)) \
                            / np.std(X, 0))) @ self.params
        return y
```

## How to use

```python
from sklearn.model_selection import train_test_split
from sklearn.datasets import load_boston


X, y = load_boston(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)
regressor = LinearRegression(learing_rate=0.03, n_iter=3000)  # you can tune hyperparameters
regressor.train(X, y)
regressor.score()  # 75%, like in scikit-learn library
```