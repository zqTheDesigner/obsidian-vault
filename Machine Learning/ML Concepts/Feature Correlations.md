# Correlations between numerical features

```python
corr_matrix = df_train.corr()
# The first one have strong positive relation, and the last one in the sorted list has the strong negative relation. 
# If the number is larger than 0.2, 0.3, consider strong relation
corr_matrix['Survived'].sort_values(ascending=False)
```

- The correlation coefficient only measures linear correlations, it may completely miss out on nonlinear relationships


# Correlations between non-numerical features

- If the non-null count columns is a lot smaller than the total entries, this columns has a lot of missing value. 
- 

```python
df_train.info()

# Visualize correlations between non numerical feature ("Sex") and numerical feature ("Survived")
sns.barplot(data=df_train, x="Sex", y="Survived")

# Visualize distrubution of certain feature, found the majority
sns.displot(df_train['Embarked'])

# Visualize the correlations
sns.barplot(data=df_train, x="Embarked", y="Survived")
```