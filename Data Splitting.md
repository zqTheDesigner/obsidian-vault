Train data split
Split train data in to x_train, x_text y_train, y_test
``` python
# X are the features, Y are the labels

X = df_train_set.iloc[:, 1:5].values
Y = df_train_set.iloc[:, 0].values


from sklearn.model_selection import train_test_split

X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=4)

print(X_train.shape)
print(Y_train.shape)
print(X_test.shape)
print(Y_test.shape)
```