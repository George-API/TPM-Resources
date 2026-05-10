# Matplotlib & Seaborn Quick Reference Guide

## Table of Contents

- [Rapid Lookup](#rapid-lookup)
- [Common Patterns](#common-patterns)

---

## Rapid Lookup

**Quick Syntax Reference**: `plt.plot()` - Line plot (matplotlib) | `plt.scatter()` - Scatter (matplotlib) | `plt.bar()` - Bar chart (matplotlib) | `plt.hist()` - Histogram (matplotlib) | `sns.boxplot()` - Box plot (seaborn) | `sns.heatmap()` - Heatmap (seaborn) | `fig, ax = plt.subplots()` - Create figure/axes (matplotlib) | `plt.savefig()` - Save figure (matplotlib)

**Note**: `plt` = matplotlib.pyplot, `sns` = seaborn

### Figure Setup
- `fig, ax = plt.subplots(figsize=(10, 6))` - Single subplot (matplotlib)
- `fig, axes = plt.subplots(nrows=2, ncols=2, figsize=(12, 8))` - Multiple subplots (matplotlib)
- `plt.figure(figsize=(10, 6))` - Create figure (matplotlib)
- `sns.set_style("whitegrid")` - Set seaborn style
- `sns.set_palette("husl")` - Set seaborn color palette
- `plt.rcParams['figure.dpi'] = 100` - Set resolution (matplotlib)

### Line Plots
- `plt.plot(x, y)` - Basic line plot (matplotlib)
- `plt.plot(x, y, label='Series', linewidth=2, linestyle='--', marker='o')` - With styling (matplotlib)
- `ax.plot(x, y, color='blue', alpha=0.7)` - Using axes object (matplotlib)
- `plt.plot(x, y1, x, y2)` - Multiple lines (matplotlib)
- `ax.plot(x, y, label='Data')` - With label (matplotlib)
- `ax.legend()` - Show legend (matplotlib)
- `sns.lineplot(data=df, x='x', y='y', hue='category')` - Seaborn line plot with grouping

### Scatter Plots
- `plt.scatter(x, y)` - Basic scatter (matplotlib)
- `plt.scatter(x, y, s=100, c=colors, alpha=0.6, cmap='viridis')` - With size/color (matplotlib)
- `sns.scatterplot(data=df, x='x', y='y', hue='category', size='value')` - Seaborn scatter with grouping
- `sns.scatterplot(data=df, x='x', y='y', hue='category', style='type')` - With style grouping (seaborn)

### Bar Charts
- `plt.bar(x, height)` - Vertical bars (matplotlib)
- `plt.barh(y, width)` - Horizontal bars (matplotlib)
- `plt.bar(x, height, color='steelblue', edgecolor='black', width=0.8)` - With styling (matplotlib)
- `sns.barplot(data=df, x='category', y='value', hue='group')` - Seaborn bar plot with grouping
- `sns.countplot(data=df, x='category')` - Count plot (seaborn)

### Histograms
- `plt.hist(data, bins=30)` - Basic histogram (matplotlib)
- `plt.hist(data, bins=30, edgecolor='black', alpha=0.7, color='steelblue')` - With styling (matplotlib)
- `plt.hist(data, bins=30, cumulative=True, density=True)` - Cumulative, normalized (matplotlib)
- `sns.histplot(data=df, x='value', bins=30, kde=True)` - Histogram with KDE overlay (seaborn)
- `sns.histplot(data=df, x='value', hue='category', multiple='stack')` - Stacked by category (seaborn)

### Box Plots
- `plt.boxplot(data)` - Basic box plot (matplotlib)
- `sns.boxplot(data=df, x='category', y='value')` - Seaborn box plot with grouping
- `sns.boxplot(data=df, x='category', y='value', hue='group')` - With multiple grouping (seaborn)
- `sns.violinplot(data=df, x='category', y='value')` - Violin plot (seaborn)

### Distribution Plots
- `sns.histplot(data=df, x='value', kde=True)` - Histogram with KDE (seaborn)
- `sns.kdeplot(data=df, x='value', hue='category')` - KDE plot (seaborn)
- `sns.rugplot(data=df, x='value')` - Rug plot (seaborn)

### Heatmaps
- `sns.heatmap(data, annot=True, fmt='.2f', cmap='viridis')` - Basic heatmap (seaborn)
- `sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', center=0, vmin=-1, vmax=1)` - Correlation matrix (seaborn)
- `sns.heatmap(data, cbar_kws={'label': 'Value'}, xticklabels=labels, yticklabels=labels)` - With labels (seaborn)

### Time Series
- `plt.plot(dates, values)` - Time series line (matplotlib)
- `ax.xaxis.set_major_formatter(mdates.DateFormatter('%Y-%m-%d'))` - Date formatting (matplotlib)
- `plt.xticks(rotation=45)` - Rotate x-axis labels (matplotlib)
- `sns.lineplot(data=df, x='date', y='value', hue='series')` - Multiple series (seaborn)

### Subplots
- `fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 5))` - Two side-by-side (matplotlib)
- `fig, axes = plt.subplots(2, 2, figsize=(12, 10))` - 2x2 grid (matplotlib)
- `axes[0, 0].plot(x, y)` - Access subplot (matplotlib)
- `plt.tight_layout()` - Adjust spacing (matplotlib)

### Styling
- `plt.title('Title', fontsize=16, fontweight='bold')` - Title (matplotlib)
- `plt.xlabel('X Label', fontsize=12)` - X-axis label (matplotlib)
- `plt.ylabel('Y Label', fontsize=12)` - Y-axis label (matplotlib)
- `plt.xlim(0, 100)` - X-axis limits (matplotlib)
- `plt.ylim(0, 100)` - Y-axis limits (matplotlib)
- `plt.xticks(rotation=45)` - Rotate x-tick labels (matplotlib)
- `plt.grid(True, alpha=0.3, linestyle='--')` - Grid (matplotlib)
- `ax.set_yscale('log')` - Log scale (matplotlib)
- `ax.set_xscale('log')` - Log scale (matplotlib)

### Colors & Markers
- `plt.plot(x, y, color='red', marker='o', markersize=8)` - Color and marker (matplotlib)
- `plt.plot(x, y, color='#FF5733', linestyle='--', linewidth=2)` - Hex color (matplotlib)
- `plt.plot(x, y, c=colors[0])` - Use color list (matplotlib)

### Annotations
- `plt.text(x, y, 'Text', fontsize=12)` - Text annotation (matplotlib)
- `plt.annotate('Note', xy=(x, y), xytext=(x+10, y+10), arrowprops=dict(arrowstyle='->'))` - Arrow annotation (matplotlib)
- `ax.axvline(x=threshold, color='red', linestyle='--', label='Threshold')` - Vertical line (matplotlib)
- `ax.axhline(y=threshold, color='red', linestyle='--', label='Threshold')` - Horizontal line (matplotlib)

### Saving
- `plt.savefig('plot.png', dpi=300, bbox_inches='tight')` - Save PNG (matplotlib)
- `plt.savefig('plot.pdf', bbox_inches='tight')` - Save PDF (matplotlib)
- `plt.savefig('plot.svg', format='svg')` - Save SVG (matplotlib)

### Display
- `plt.show()` - Show plot (matplotlib)
- `plt.close()` - Close figure (matplotlib)
- `plt.clf()` - Clear figure (matplotlib)

---

## Common Patterns

### Basic EDA Plot Grid

```python
fig, axes = plt.subplots(2, 2, figsize=(12, 10))

# Histogram
axes[0, 0].hist(df['value'], bins=30, edgecolor='black', alpha=0.7)
axes[0, 0].set_title('Distribution')
axes[0, 0].set_xlabel('Value')

# Box plot
sns.boxplot(data=df, x='category', y='value', ax=axes[0, 1])
axes[0, 1].set_title('By Category')

# Scatter
axes[1, 0].scatter(df['x'], df['y'], alpha=0.6)
axes[1, 0].set_xlabel('X')
axes[1, 0].set_ylabel('Y')

# Time series
axes[1, 1].plot(df['date'], df['value'])
axes[1, 1].set_xlabel('Date')
axes[1, 1].set_ylabel('Value')
axes[1, 1].tick_params(axis='x', rotation=45)

plt.tight_layout()
plt.savefig('eda_grid.png', dpi=300, bbox_inches='tight')
```

### Correlation Heatmap

```python
corr = df.select_dtypes(include=[np.number]).corr()
sns.heatmap(corr, annot=True, fmt='.2f', cmap='coolwarm', center=0, 
            square=True, linewidths=0.5, cbar_kws={'shrink': 0.8})
plt.title('Correlation Matrix', fontsize=14, fontweight='bold')
plt.tight_layout()
plt.savefig('correlation.png', dpi=300, bbox_inches='tight')
```

### Multi-Series Line Plot

```python
fig, ax = plt.subplots(figsize=(12, 6))
for series in df['series'].unique():
    subset = df[df['series'] == series]
    ax.plot(subset['date'], subset['value'], label=series, marker='o', markersize=4)

ax.set_xlabel('Date', fontsize=12)
ax.set_ylabel('Value', fontsize=12)
ax.set_title('Time Series Comparison', fontsize=14, fontweight='bold')
ax.legend(loc='best')
ax.grid(True, alpha=0.3)
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig('timeseries.png', dpi=300, bbox_inches='tight')
```

### Distribution Comparison

```python
fig, axes = plt.subplots(1, 2, figsize=(12, 5))

# Histogram comparison
for category in df['category'].unique():
    data = df[df['category'] == category]['value']
    axes[0].hist(data, alpha=0.6, label=category, bins=30)
axes[0].set_xlabel('Value')
axes[0].set_ylabel('Frequency')
axes[0].legend()
axes[0].set_title('Histogram Comparison')

# KDE comparison
sns.kdeplot(data=df, x='value', hue='category', ax=axes[1])
axes[1].set_xlabel('Value')
axes[1].set_ylabel('Density')
axes[1].set_title('Density Comparison')

plt.tight_layout()
plt.savefig('distributions.png', dpi=300, bbox_inches='tight')
```

### Grouped Bar Chart

```python
pivot = df.pivot_table(index='category', columns='group', values='value', aggfunc='mean')
pivot.plot(kind='bar', figsize=(10, 6), width=0.8, edgecolor='black')
plt.xlabel('Category', fontsize=12)
plt.ylabel('Value', fontsize=12)
plt.title('Grouped Bar Chart', fontsize=14, fontweight='bold')
plt.legend(title='Group')
plt.xticks(rotation=45)
plt.tight_layout()
plt.savefig('grouped_bars.png', dpi=300, bbox_inches='tight')
```

### Faceted Plots (Seaborn)

```python
g = sns.FacetGrid(df, col='category', col_wrap=3, height=4, aspect=1.2)
g.map_dataframe(sns.scatterplot, x='x', y='y', hue='group', alpha=0.7)
g.add_legend()
g.set_axis_labels('X', 'Y')
plt.savefig('faceted.png', dpi=300, bbox_inches='tight')
```

### Pair Plot

```python
sns.pairplot(df[['x', 'y', 'z', 'category']], hue='category', diag_kind='kde')
plt.savefig('pairplot.png', dpi=300, bbox_inches='tight')
```

### Custom Styling Function

```python
def style_plot(ax, title, xlabel, ylabel):
    ax.set_title(title, fontsize=14, fontweight='bold')
    ax.set_xlabel(xlabel, fontsize=12)
    ax.set_ylabel(ylabel, fontsize=12)
    ax.grid(True, alpha=0.3, linestyle='--')
    ax.spines['top'].set_visible(False)
    ax.spines['right'].set_visible(False)

fig, ax = plt.subplots(figsize=(10, 6))
ax.plot(x, y)
style_plot(ax, 'My Plot', 'X Axis', 'Y Axis')
plt.tight_layout()
```

