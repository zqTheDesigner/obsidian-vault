---
tags: data-visualization 
---
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

## Adjust the spacing around plots

```python
# Shrink the spacing all the way to 0

fig, axes = plt.subplots(2,2, sharex=True, sharey=True)

  

for i in range(2):

for j in range(2):

axes[i, j].hist(np.random.standard_normal(500), bins=50, color="black", alpha=0.5)



fig.subplots_adjust(wspace=0, hspace=0)
```

## Colors, Markers and Line-Styles
#matplotlib/line-styles

plot x versus y with green dashes

`ax.plot(x, y, linestyle="--"l color="green")`

Line plots can additionally have markers to highlight the actual data points

```python
fig = plt.figure()
ax = fig.add_subplot()
```

```python
fig, ax = plt.subplots(1,1)

x = np.arange(10)
y = np.random.standard_normal(10) 

ax.plot(x, y, linestyle="--", marker=".", label="default")

# Use drawstyle to change how the sequence is interpolated
ax.plot(x, y, drawstyle="steps-post", label="steps-post")

# Call legend method to show the labels
ax.legend()
```

## Ticks, Labels and Legends
#matplotlib/ticks #matplotlib/labels #matplotlib/legends

Most kinds of plot decorations can be accessed through methods on matplotlib axes objects.
xlim, xticks, xticklabels

Controls the plot range, tick locations and tick labels

All such methods act on the active or most recently created AxesSubplot

### Setting the title, axis labels, ticks and tick labels

```python
fig, ax = plt.subplots()

ax.plot(np.random.standard_normal(1000).cumsum())

ticks = ax.set_xticks([0, 250, 500, 750, 1000])

# Use set_xticks and set_xticklabels to place the ticks along the datarange
# the rotation option sets the x tick label at 30 degree rotaton
labels = ax.set_xticklabels(
    ["one", "two", "three", "four", "five"], rotation=30, fontsize=8
)

# Give a name to the x-axis
ax.set_xlabel('Stages')

# Set the subplot title
ax.set_title('My first matplotlib plot')

# Use set method to batch setting plot properties
ax.set(title="My first matplot plot", xlabel="Stages", ylabel="Value")
```

### Adding Legends
#matplotlib/legends 
Pass the label argument when adding each piece of the plot

To exclude one or more elements from the legend, pass no label or `label="_nolegend_"`

```python
fig, ax = plt.subplots()

ax.plot(np.random.randn(1000).cumsum(), color="black", label="one")
ax.plot(np.random.randn(1000).cumsum(), color="black", label="two", linestyle='--')
ax.plot(np.random.randn(1000).cumsum(), color="black", label="three", linestyle='dotted')

# Call the legend method to create a legend
# use ax.legend? doc string to check the documentation
# pass in loc parameters to set the location of the legend
ax.legend(loc="upper right")

```

## Annotations and Drawings on a Subplot
#matplotlib/annotations 


Plot annotations: text, arrows, other shapes
Add annotations with `text`, `arrow` and `annotate` functions

`ax.text(a, y, 'Hello world', family='monospace', fontsize=10)`

```python
from datetime import datetime

fig, ax = plt.subplots()

data = pd.read_csv("./datasets/spx.csv", index_col=0, parse_dates=True)

spx = data["SPX"]

spx.plot(ax=ax, color="black")

crisis_data = [
    (datetime(2007, 10, 11), "Peak of bull market"),
    (datetime(2008, 3, 12), "Bear Sterns Fils"),
    (datetime(2008, 9, 15), "Lehman Bankruptcy"),
]

# ax.annotate method can draw labels at the indicated x and y coordinates
for date, label in crisis_data:
    ax.annotate(
        label,
        xy=(date, spx.asof(date) + 75),
        xytext=(date, spx.asof(date) + 225),
        arrowprops=dict(facecolor="black", headwidth=2, width=2, headlength=4),
        horizontalalignment="left",
        verticalalignment="top",
    )

# Zoom in on 2007 - 2010, manually set the start and end boundaries for the plot
ax.set_xlim(["1/1/2007", "1/1/2011"])
ax.set_ylim([600, 1800])

ax.set_title("Important dates in the 2008 to 2001")

```

#### Drawing shapes
#matplotlib/drawings 

matplotlib `patches` object represent common shapes
Full set can be found in `matplotlib.patches`

To add a shape to a plot, you create the patch object and add it to a subplot ax by passing the patch to ax.add_patch

```python
fig, ax = plt.subplots()

rect = plt.Rectangle((0.2, 0.75), 0.4, 0.15, color="black", alpha=0.3)
circ = plt.Circle((0.7, 0.2), 0.15, color="blue", alpha=0.3)
pgon = plt.Polygon([[0.15, 0.15], [0.34, 0.4], [0.2, 0.6]], color="green", alpha=0.5)

ax.add_patch(rect)
ax.add_patch(circ)
ax.add_patch(pgon)

```

## Saving Plots to File
#matplotlib/save-to-file

Save the active figure using figure object's `savefig` instance method.

`fig.savefig('figpath.pmg', dpi=400)`

The fie type is inferred from the file extension

## matplotlib Configuration
#matplotlib/configuration

Use `plt.rc` method to modify the configuration 
`plt.rc('figure', figsize=(10,10))`

First argument to rc is the component you wish to customize 
Followed by a sequence of keyword arguments indicating the new parameters

`plt.rc('font', family='monospace', weight='bold', size=8)`

All current configuration settings can be found in `plt.rcParams`

matplotlib comes with a configuration file `matplotlibrc` in `matplotlib/mpl-data` directory. 
customise this file and place in home directory titled .matplotlibrc, it will be loaded each you use matplotlib 

# 9.2 Plotting with pandas and seaborn
#pandas/plotting #seaborn

## Line Plots

```python
s = pd.Series(np.random.standard_normal(10).cumsum(), index=np.arange(0, 100, 10))

# By default, plot() makes a line plot
s.plot()
```

Series.plot method arguments

  

Series.plot method arguments

| Argument | Description |
|-|-| 
| label | Label for plot legend |
| ax | matplotlib subplot object to plot on, if nothing passed, use active matplotlib subplot |
| style | style string, like 'ko--' to be passed to matplotlib |
| alpha | The plot fill opacity (from 0 to 1) |
| kind | Can be "area", 'bar', 'barh', 'density', 'hist', 'kde', 'line' or 'pie', default to 'line' |
| figsize | Size of the figure object to create |
| logx | Pass True for logarithmic scaling on the x axis; pass 'sym' for symmetric logarithm that permits negative value |
| logy | Pass True for logarithmic scaling on the y axis | 
| title | title to use ofr the plot | 
| use_index | use the object index for tick labels |
| rot | rotation of tick labels (0 to 360) |
| xticks | Values to use for x-axis ticks |
| yticks | Values to use for y-axis ticks | 
| xlim | x-axis limits (e.g. [0, 10]) |
| ylim | y-axis limits |
| gird | display axis grid, off by default | 