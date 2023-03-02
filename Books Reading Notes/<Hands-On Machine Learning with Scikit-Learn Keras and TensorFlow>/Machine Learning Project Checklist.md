- Check assumptions (list and verify)
- Check amount of entries (rows), columns and memory usage
- Check amount of missing values
- Check data types
- Any categorical attribute? Ordinal or one-hot?
- Check summary of numeric attributes (describe())
- Check histogram for each numerical attribute
- Check none-numerical attribute

- Create a test set

- Discover and visualize the data

- Looking for correlations

- Experimenting with attribute combinations (feature engineering)

# Prepare the data
- Handling the missing value
- Handling text and categorical attributes
- Split training and testing data
- Build data processing pipeline

# Select model
- K-fold validation (evaluate different model)
- Grid search (evaluate different hyperparameters)
- Use `joblib.dump()` to save the model
- 