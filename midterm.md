# Midterm Exam Correction

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
**The optimal alpha value for this Lasso regression is 0.00186.**

**Reflection:**

Although my original code was almost the same as displayed above, it kept returning 0.00100 for the optimal alpha value. However, when I cleared all the output and only ran the cell for the Lasso regression again, it returned  the correct answer 0.00186. I am not really sure what caused this problem, but I think it may be associated with the data stored for each variable in the memory space. Some variables were kept overwritten when I ran different codes for each question, so when I got to question 24, the data stored in the variable I need may not be the original one, which will lead to a different optimal alpha value. That's why if I only run the cell for question 24 after clearing all the output, it returns the correct value. 


