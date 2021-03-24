# Mid Term Correction

### Question 24: If we had looked at MSE instead of R2 when doing our Lasso regression (question 20), what would we have determined the optimal value for alpha to be? Enter your answer to 5 decimal places, for example: 0.12345

```python
def DoKFold(model, X, y, k, standardize=False, random_state=146):
    from sklearn.model_selection import KFold
    
    if standardize:
        from sklearn.preprocessing import StandardScaler as SS
        ss = SS()

    kf = KFold(n_splits=k, shuffle=True, random_state=random_state)

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
            Xtrain = ss.fit_transform(Xtrain) # compute parameters of feature scaling based on training data
            Xtest = ss.transform(Xtest) #standerdize testing data then 

        model.fit(Xtrain, ytrain)

        train_scores.append(model.score(Xtrain, ytrain))
        test_scores.append(model.score(Xtest, ytest))
        
        ytrain_pred = model.predict(Xtrain)
        ytest_pred = model.predict(Xtest)
        
        train_mse.append(np.mean((ytrain-ytrain_pred)**2))
        test_mse.append(np.mean((ytest-ytest_pred)**2)) 
        
    return train_scores, test_scores, train_mse, test_mse
```

```python
a_range = np.linspace(0.001, 0.003, 101)
k = 20
avg_te_mse=[]

for a in a_range:
    las_reg = Lasso(alpha=a)
    train_scores, test_scores, train_mse, test_mse = DoKFold(las_reg,x,y,k,standardize=True)
    avg_te_mse.append(np.mean(test_mse))

idx = np.argmin(avg_te_mse)
print('Optimal alpha value: ' + format(a_range[idx], '.10f'))
print('Testing score for this value: ' + format(avg_te_mse[idx], '.5f'))
```


