Seaborn - matplotlib add on toolkit
to output plots in the Jupyter notebook: `%matplotlib inline`

# 9.1 A brief matplotlib API Primer

```python
%matplotlib inline

data = np.arange(10)
plt.plot(data)
```

## Figures and Subplots

Plots in matplotlib reside within a Figure object.
To create a new figure use plt.figure
`fig = plt.figure()`

figsize will guarantee the figure has a certain size and aspect ratio of saved to disk
You can't make a plot with a blank figure. You have to create subplots using add_subplot
`ax1 = fig.add_subplot(2, 2, 1)`
It is preferred to use the axis methods over the top-level plotting functions like plt.plot

```python
fig = plt.figure()

# Figure should be 2 x 2, up to 4 plots in total, 1st figure been selected
# plot axis objects have various methods that create different types of plots
ax1 = fig.add_subplot(2, 2, 1)
# ax2 = fig.add_subplot(2, 2, 2)
ax2 = fig.add_subplot(2, 2, 4)

ax2.plot(np.random.standard_normal(50).cumsum(),color="black", linestyle="dashed")
```

The objects returned by fig.add_subplot here are AxesSubplot objects.
You can directly plot on the other empty subplots by calling each one's instance method

```python
# alpha is the transprancy of the overlaid plot
ax1.hist(np.random.standard_normal(100), bins=20, color='black', alpha=0.3)
ax3.scatter(np.arange(30), np.arange(30) + 3 * np.random.standard_normal(30))

fig # in jupyter notebook, call the fig again to show the charts
```
Use plt.subplots method to create a new figure and returns a NumPy array containing the created subplot objects
```python
fig, axes = plt.subplots(2, 3)

# axes is a NumPy array of list of subplot objects
axes
# Same as when create ax1 to ax6 elements with fig.add_subplot()
```