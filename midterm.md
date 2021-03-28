# Midterm Exam 

### Import libraries and dataset
```python
from sklearn.datasets import fetch_california_housing
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import KFold
from sklearn.linear_model import Ridge
from sklearn.linear_model import Lasso
```

```python
data = fetch_california_housing()
X=data.data
X_names = data.feature_names
y=data.target  
X_df = pd.DataFrame(data = X, columns=X_names)
```
### Create DoKFold function
```python
def DoKFold(model, X, y, k, standardize=False, random_state=146):
    import numpy as np
    from sklearn.model_selection import KFold
    
    kf = KFold(n_splits=k, shuffle=True, random_state=random_state)

    if standardize:
        from sklearn.preprocessing import StandardScaler as SS
        ss = SS()

    train_scores = [] 
    test_scores = []  
    
    train_mse = []   
    test_mse = []   

    for idxTrain, idxTest in kf.split(X):
        Xtrain = X[idxTrain, :]
        Xtest = X[idxTest, :]
        ytrain = y[idxTrain]
        ytest = y[idxTest]

        if standardize:
            Xtrain = ss.fit_transform(Xtrain) 
            Xtest = ss.transform(Xtest) 

        model.fit(Xtrain, ytrain)

        train_scores.append(model.score(Xtrain, ytrain))
        test_scores.append(model.score(Xtest, ytest))
        
        ytrain_pred = model.predict(Xtrain)
        ytest_pred = model.predict(Xtest)
        
        train_mse.append(np.mean((ytrain-ytrain_pred)**2))
        test_mse.append(np.mean((ytest-ytest_pred)**2)) 
        
    return train_scores, test_scores, train_mse, test_mse
```

### Question 15: 
Most strong correlated feature is the MedInc (median income).
```python
df=X_df.copy()
df['MedHouseVal']=y
df.corr() 
```

### Question 16:
If the features are standardized, the correlations from the previous question do not change.
```python
ss = StandardScaler()
X_scaled = ss.fit_transform(X)
df_scaled = pd.DataFrame(X_scaled, columns=X_names)
df_scaled['MedHouseVal']=y
df_scaled.corr()
```

### Question 17:
R^2 = 0.47.
```python
np.round(np.corrcoef(X_df['MedInc'],y)[0][1]**2, 2)
```

### Question 18:
Mean R^2 value on the test folds for the linear regression is 0.6198.
```python
k=20
lin_reg = LinearRegression()
train_scores, test_scores, train_mse, test_mse = DoKFold(lin_reg,X,y,k,standardize=True)
print(np.mean(train_scores), np.mean(test_scores))
print(np.mean(train_mse), np.mean(test_mse))
```
### Question 19:
Mean R^2 value on the test folds for the ridge regression is 0.60201.
```python
rid_a_range = np.linspace(20, 30, 101)
k = 20

rid_tr=[]
rid_te=[]
rid_tr_mse=[]
rid_te_mse=[]

for a in rid_a_range:
    rid_reg = Ridge(alpha=a) 
    train_scores, test_scores, train_mse, test_mse= DoKFold(rid_reg,X,y,k,standardize=True)
    rid_tr.append(np.mean(train_scores))
    rid_te.append(np.mean(test_scores))
    rid_tr_mse.append(np.mean(train_mse))
    rid_te_mse.append(np.mean(test_mse))
    
idx = np.argmax(rid_te)
print(rid_a_range[idx], rid_tr[idx], rid_te[idx], rid_tr_mse[idx], rid_te_mse[idx])

plt.plot(rid_a_range, rid_te,'or')
plt.xlabel('$\\alpha$')
plt.ylabel('Avg $R^2$')
plt.show()
```

### Question 20:
Mean R^2 value on the test folds for the Lasso regression is 0.60213.
```python
las_a_range = np.linspace(0.001, 0.003, 101)
k = 20

las_tr=[]
las_te=[]
las_tr_mse=[]
las_te_mse=[]

for a in las_a_range:
    las_reg = Lasso(alpha=a) 
    train_scores, test_scores, train_mse, test_mse= DoKFold(las_reg,X,y,k,standardize=True)
    las_tr.append(np.mean(train_scores))
    las_te.append(np.mean(test_scores))
    las_tr_mse.append(np.mean(train_mse))
    las_te_mse.append(np.mean(test_mse))

idx = np.argmax(las_te)
print(las_a_range[idx], las_tr[idx], las_te[idx], las_tr_mse[idx], las_te_mse[idx])

plt.plot(las_a_range, las_te,'or')
plt.xlabel('$\\alpha$')
plt.ylabel('Avg $R^2$')
plt.show()
```

### Question 21:
Lasso model estimates the smallest coefficient for the variable that is least correlated.
```python
lin = LinearRegression()  
rid = Ridge(alpha=25.8)
las = Lasso(alpha=0.00186)

lin.fit(X_scaled,y)
rid.fit(X_scaled,y)
las.fit(X_scaled,y)

print(lin.coef_[5],rid.coef_[5],las.coef_[5])
```
### Question 22:
Lasso model estimates the smallest coefficient for the variable that is most correlated.
```python
print(lin.coef_[0],rid.coef_[0],las.coef_[0])
```

### Question 23: 
The optimal alpha value (=26.1) is different from that in Q19 (=25.8).
```python
idx = np.argmin(rid_te_mse)
print(rid_a_range[idx], rid_tr[idx], rid_te[idx], rid_tr_mse[idx], rid_te_mse[idx])

plt.plot(rid_a_range, rid_te_mse,'or')
plt.xlabel('$\\alpha$')
plt.ylabel('Avg MSE')
plt.show()
```





