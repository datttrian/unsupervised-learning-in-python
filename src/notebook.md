# Unsupervised Learning in Python

## Clustering for Dataset Exploration

### Clustering 2D points

From the scatter plot of the previous exercise, you saw that the points
seem to separate into 3 clusters. You'll now create a KMeans model to
find 3 clusters, and fit it to the data points from the previous
exercise. After the model has been fit, you'll obtain the cluster labels
for some new points using the `.predict()` method.

You are given the array `points` from the previous exercise, and also an
array `new_points`.

**Instructions**

- Import `KMeans` from `sklearn.cluster`.
- Using `KMeans()`, create a `KMeans` instance called `model` to find
  `3` clusters. To specify the number of clusters, use the `n_clusters`
  keyword argument.
- Use the `.fit()` method of `model` to fit the model to the array of
  points `points`.
- Use the `.predict()` method of `model` to predict the cluster labels
  of `new_points`, assigning the result to `labels`.
- Hit submit to see the cluster labels of `new_points`.

**Answer**

```{python}
# Import KMeans
from sklearn.cluster import KMeans

# Create a KMeans instance with 3 clusters: model
model = KMeans(n_clusters=3)

# Fit model to points
model.fit(points)

# Determine the cluster labels of new_points: labels
labels = model.predict(new_points)

# Print cluster labels of new_points
print(labels)
```

### Inspect your clustering

Let's now inspect the clustering you performed in the previous exercise!

A solution to the previous exercise has already run, so `new_points` is
an array of points and `labels` is the array of their cluster labels.

**Instructions**

- Import `matplotlib.pyplot` as `plt`.
- Assign column `0` of `new_points` to `xs`, and column `1` of
  `new_points` to `ys`.
- Make a scatter plot of `xs` and `ys`, specifying the `c=labels`
  keyword arguments to color the points by their cluster label. Also
  specify `alpha=0.5`.
- Compute the coordinates of the centroids using the `.cluster_centers_`
  attribute of `model`.
- Assign column `0` of `centroids` to `centroids_x`, and column `1` of
  `centroids` to `centroids_y`.
- Make a scatter plot of `centroids_x` and `centroids_y`, using `'D'` (a
  diamond) as a marker by specifying the `marker` parameter. Set the
  size of the markers to be `50` using `s=50`.

**Answer**

```{python}
# Import pyplot
from matplotlib import pyplot as plt

# Assign the columns of new_points: xs and ys
xs = new_points[:,0]
ys = new_points[:,1]

# Make a scatter plot of xs and ys, using labels to define the colors
plt.scatter(xs, ys, c=labels, alpha=0.5)

# Assign the cluster centers: centroids
centroids = model.cluster_centers_

# Assign the columns of centroids: centroids_x, centroids_y
centroids_x = centroids[:,0]
centroids_y = centroids[:,1]

# Make a scatter plot of centroids_x and centroids_y
plt.scatter(centroids_x, centroids_y, marker='D', s=50)
plt.show()
```

### How many clusters of grain?

In the video, you learned how to choose a good number of clusters for a
dataset using the k-means inertia graph. You are given an array
`samples` containing the measurements (such as area, perimeter, length,
and several others) of samples of grain. What's a good number of
clusters in this case?

`KMeans` and PyPlot (`plt`) have already been imported for you.

This dataset was sourced from the [UCI Machine Learning
Repository](https://archive.ics.uci.edu/ml/datasets/seeds).

**Instructions**

- For each of the given values of `k`, perform the following steps:
- Create a `KMeans` instance called `model` with `k` clusters.
- Fit the model to the grain data `samples`.
- Append the value of the `inertia_` attribute of `model` to the list
  `inertias`.
- The code to plot `ks` vs `inertias` has been written for you, so hit
  submit to see the plot!

**Answer**

```{python}
ks = range(1, 6)
inertias = []

for k in ks:
    # Create a KMeans instance with k clusters: model
    model = KMeans(n_clusters=k)
    
    # Fit model to samples
    model.fit(samples)
    
    # Append the inertia to the list of inertias
    inertias.append(model.inertia_)
    
# Plot ks vs inertias
plt.plot(ks, inertias, '-o')
plt.xlabel('number of clusters, k')
plt.ylabel('inertia')
plt.xticks(ks)
plt.show()
```

### Evaluating the grain clustering

In the previous exercise, you observed from the inertia plot that 3 is a
good number of clusters for the grain data. In fact, the grain samples
come from a mix of 3 different grain varieties: "Kama", "Rosa" and
"Canadian". In this exercise, cluster the grain samples into three
clusters, and compare the clusters to the grain varieties using a
cross-tabulation.

You have the array `samples` of grain samples, and a list `varieties`
giving the grain variety for each sample. Pandas (`pd`) and `KMeans`
have already been imported for you.

**Instructions**

- Create a `KMeans` model called `model` with `3` clusters.
- Use the `.fit_predict()` method of `model` to fit it to `samples` and
  derive the cluster labels. Using `.fit_predict()` is the same as using
  `.fit()` followed by `.predict()`.
- Create a DataFrame `df` with two columns named `'labels'` and
  `'varieties'`, using `labels` and `varieties`, respectively, for the
  column values. This has been done for you.
- Use the `pd.crosstab()` function on `df['labels']` and
  `df['varieties']` to count the number of times each grain variety
  coincides with each cluster label. Assign the result to `ct`.
- Hit submit to see the cross-tabulation!

**Answer**

```{python}
# Create a KMeans model with 3 clusters: model
model = KMeans(n_clusters=3)

# Use fit_predict to fit model and obtain cluster labels: labels
labels = model.fit_predict(samples)

# Create a DataFrame with clusters and varieties as columns: df
df = pd.DataFrame({'labels': labels, 'varieties': varieties})

# Create crosstab: ct
ct = pd.crosstab(df['labels'], df['varieties'])

# Display ct
print(ct)
```

### Scaling fish data for clustering

You are given an array `samples` giving measurements of fish. Each row
represents an individual fish. The measurements, such as weight in
grams, length in centimeters, and the percentage ratio of height to
length, have very different scales. In order to cluster this data
effectively, you'll need to standardize these features first. In this
exercise, you'll build a pipeline to standardize and cluster the data.

These fish measurement data were sourced from the [Journal of Statistics
Education](http://ww2.amstat.org/publications/jse/jse_data_archive.htm).

**Instructions**

- Import:
  - `make_pipeline` from `sklearn.pipeline`.
  - `StandardScaler` from `sklearn.preprocessing`.
  - `KMeans` from `sklearn.cluster`.
- Create an instance of `StandardScaler` called `scaler`.
- Create an instance of `KMeans` with `4` clusters called `kmeans`.
- Create a pipeline called `pipeline` that chains `scaler` and `kmeans`.
  To do this, you just need to pass them in as arguments to
  `make_pipeline()`.

**Answer**

```{python}
# Perform the necessary imports
from sklearn.pipeline import make_pipeline
from sklearn.preprocessing import StandardScaler
from sklearn.cluster import KMeans

# Create scaler: scaler
scaler = StandardScaler()

# Create KMeans instance: kmeans
kmeans = KMeans(n_clusters=4)

# Create pipeline: pipeline
pipeline = make_pipeline(scaler, kmeans)
```

### Clustering the fish data

You'll now use your standardization and clustering pipeline from the
previous exercise to cluster the fish by their measurements, and then
create a cross-tabulation to compare the cluster labels with the fish
species.

As before, `samples` is the 2D array of fish measurements. Your pipeline
is available as `pipeline`, and the species of every fish sample is
given by the list `species`.

**Instructions**

- Import `pandas` as `pd`.
- Fit the pipeline to the fish measurements `samples`.
- Obtain the cluster labels for `samples` by using the `.predict()`
  method of `pipeline`.
- Using `pd.DataFrame()`, create a DataFrame `df` with two columns named
  `'labels'` and `'species'`, using `labels` and `species`,
  respectively, for the column values.
- Using `pd.crosstab()`, create a cross-tabulation `ct` of
  `df['labels']` and `df['species']`.

**Answer**

```{python}
# Import pandas
import pandas as pd

# Fit the pipeline to samples
pipeline.fit(samples)

# Calculate the cluster labels: labels
labels = pipeline.predict(samples)

# Create a DataFrame with labels and species as columns: df
df = pd.DataFrame({'labels': labels, 'species': species})

# Create crosstab: ct
ct = pd.crosstab(df['labels'], df['species'])

# Display ct
print(ct)
```

### Clustering stocks using KMeans

In this exercise, you'll cluster companies using their daily stock price
movements (i.e. the dollar difference between the closing and opening
prices for each trading day). You are given a NumPy array `movements` of
daily price movements from 2010 to 2015 (obtained from Yahoo! Finance),
where each row corresponds to a company, and each column corresponds to
a trading day.

Some stocks are more expensive than others. To account for this, include
a `Normalizer` at the beginning of your pipeline. The Normalizer will
separately transform each company's stock price to a relative scale
before the clustering begins.

Note that `Normalizer()` is different to `StandardScaler()`, which you
used in the previous exercise. While `StandardScaler()` standardizes
**features** (such as the features of the fish data from the previous
exercise) by removing the mean and scaling to unit variance,
`Normalizer()` rescales **each sample** - here, each company's stock
price - independently of the other.

`KMeans` and `make_pipeline` have already been imported for you.

**Instructions**

- Import `Normalizer` from `sklearn.preprocessing`.
- Create an instance of `Normalizer` called `normalizer`.
- Create an instance of `KMeans` called `kmeans` with `10` clusters.
- Using `make_pipeline()`, create a pipeline called `pipeline` that
  chains `normalizer` and `kmeans`.
- Fit the pipeline to the `movements` array.

**Answer**

```{python}
# Import Normalizer
from sklearn.preprocessing import Normalizer

# Create a normalizer: normalizer
normalizer = Normalizer()

# Create a KMeans model with 10 clusters: kmeans
kmeans = KMeans(n_clusters=10)

# Make a pipeline chaining normalizer and kmeans: pipeline
pipeline = make_pipeline(normalizer, kmeans)

# Fit pipeline to the daily price movements
pipeline.fit(movements)
```

### Which stocks move together?

In the previous exercise, you clustered companies by their daily stock
price movements. So which company have stock prices that tend to change
in the same way? You'll now inspect the cluster labels from your
clustering to find out.

Your solution to the previous exercise has already been run. Recall that
you constructed a Pipeline `pipeline` containing a `KMeans` model and
fit it to the NumPy array `movements` of daily stock movements. In
addition, a list `companies` of the company names is available.

**Instructions**

- Import `pandas` as `pd`.
- Use the `.predict()` method of the pipeline to predict the labels for
  `movements`.
- Align the cluster labels with the list of company names `companies` by
  creating a DataFrame `df` with `labels` and `companies` as columns.
  This has been done for you.
- Use the `.sort_values()` method of `df` to sort the DataFrame by the
  `'labels'` column, and print the result.
- Hit submit and take a moment to see which companies are together in
  each cluster!

**Answer**

```{python}
# Import pandas
import pandas as pd

# Predict the cluster labels: labels
labels = pipeline.predict(movements)

# Create a DataFrame aligning labels and companies: df
df = pd.DataFrame({'labels': labels, 'companies': companies})

# Display df sorted by cluster label
print(df.sort_values('labels'))
```

## Visualization with Hierarchical Clustering and t-SNE

### Hierarchical clustering of the grain data

In the video, you learned that the SciPy `linkage()` function performs
hierarchical clustering on an array of samples. Use the `linkage()`
function to obtain a hierarchical clustering of the grain samples, and
use `dendrogram()` to visualize the result. A sample of the grain
measurements is provided in the array `samples`, while the variety of
each grain sample is given by the list `varieties`.

**Instructions**

- Import:
  - `linkage` and `dendrogram` from `scipy.cluster.hierarchy`.
  - `matplotlib.pyplot` as `plt`.
- Perform hierarchical clustering on `samples` using the `linkage()`
  function with the `method='complete'` keyword argument. Assign the
  result to `mergings`.
- Plot a dendrogram using the `dendrogram()` function on `mergings`.
  Specify the keyword arguments `labels=varieties`, `leaf_rotation=90`,
  and `leaf_font_size=6`.

**Answer**

```{python}
# Perform the necessary imports
from scipy.cluster.hierarchy import linkage, dendrogram
import matplotlib.pyplot as plt

# Calculate the linkage: mergings
mergings = linkage(samples, method='complete')

# Plot the dendrogram, using varieties as labels
dendrogram(mergings,
           labels=varieties,
           leaf_rotation=90,
           leaf_font_size=6,
)
plt.show()
```

### Hierarchies of stocks

In chapter 1, you used k-means clustering to cluster companies according
to their stock price movements. Now, you'll perform hierarchical
clustering of the companies. You are given a NumPy array of price
movements `movements`, where the rows correspond to companies, and a
list of the company names `companies`. SciPy hierarchical clustering
doesn't fit into a sklearn pipeline, so you'll need to use the
`normalize()` function from `sklearn.preprocessing` instead of
`Normalizer`.

`linkage` and `dendrogram` have already been imported from
`scipy.cluster.hierarchy`, and PyPlot has been imported as `plt`.

**Instructions**

- Import `normalize` from `sklearn.preprocessing`.
- Rescale the price movements for each stock by using the `normalize()`
  function on `movements`.
- Apply the `linkage()` function to `normalized_movements`, using
  `'complete'` linkage, to calculate the hierarchical clustering. Assign
  the result to `mergings`.
- Plot a dendrogram of the hierarchical clustering, using the list
  `companies` of company names as the `labels`. In addition, specify the
  `leaf_rotation=90`, and `leaf_font_size=6` keyword arguments as you
  did in the previous exercise.

**Answer**

```{python}
# Import normalize
from sklearn.preprocessing import normalize

# Normalize the movements: normalized_movements
normalized_movements = normalize(movements)

# Calculate the linkage: mergings
mergings = linkage(normalized_movements, method='complete')

# Plot the dendrogram
dendrogram(
    mergings,
    labels=companies,
    leaf_rotation=90,
    leaf_font_size=6
)
plt.show()
```

### Different linkage, different hierarchical clustering!

In the video, you saw a hierarchical clustering of the voting countries
at the Eurovision song contest using `'complete'` linkage. Now, perform
a hierarchical clustering of the voting countries with `'single'`
linkage, and compare the resulting dendrogram with the one in the video.
Different linkage, different hierarchical clustering!

You are given an array `samples`. Each row corresponds to a voting
country, and each column corresponds to a performance that was voted
for. The list `country_names` gives the name of each voting country.
This dataset was obtained from
[Eurovision](https://www.eurovision.tv/page/results).

**Instructions**

- Import `linkage` and `dendrogram` from `scipy.cluster.hierarchy`.
- Perform hierarchical clustering on `samples` using the `linkage()`
  function with the `method='single'` keyword argument. Assign the
  result to `mergings`.
- Plot a dendrogram of the hierarchical clustering, using the list
  `country_names` as the `labels`. In addition, specify the
  `leaf_rotation=90`, and `leaf_font_size=6` keyword arguments as you
  have done earlier.

**Answer**

```{python}
# Perform the necessary imports
import matplotlib.pyplot as plt
from scipy.cluster.hierarchy import linkage, dendrogram

# Calculate the linkage: mergings
mergings = linkage(samples, method='single')

# Plot the dendrogram
dendrogram(mergings,
           labels=country_names,
           leaf_rotation=90,
           leaf_font_size=6,
)
plt.show()
```

### Extracting the cluster labels

In the previous exercise, you saw that the intermediate clustering of
the grain samples at height 6 has 3 clusters. Now, use the `fcluster()`
function to extract the cluster labels for this intermediate clustering,
and compare the labels with the grain varieties using a
cross-tabulation.

The hierarchical clustering has already been performed and `mergings` is
the result of the `linkage()` function. The list `varieties` gives the
variety of each grain sample.

**Instructions**

- Import:
  - `pandas` as `pd`.
  - `fcluster` from `scipy.cluster.hierarchy`.
- Perform a flat hierarchical clustering by using the `fcluster()`
  function on `mergings`. Specify a maximum height of `6` and the
  keyword argument `criterion='distance'`.
- Create a DataFrame `df` with two columns named `'labels'` and
  `'varieties'`, using `labels` and `varieties`, respectively, for the
  column values. This has been done for you.
- Create a cross-tabulation `ct` between `df['labels']` and
  `df['varieties']` to count the number of times each grain variety
  coincides with each cluster label.

**Answer**

```{python}
# Perform the necessary imports
import pandas as pd
from scipy.cluster.hierarchy import fcluster

# Use fcluster to extract labels: labels
labels = fcluster(mergings, 6, criterion='distance')

# Create a DataFrame with labels and varieties as columns: df
df = pd.DataFrame({'labels': labels, 'varieties': varieties})

# Create crosstab: ct
ct = pd.crosstab(df['labels'], df['varieties'])

# Display ct
print(ct)
```

### t-SNE visualization of grain dataset

In the video, you saw t-SNE applied to the iris dataset. In this
exercise, you'll apply t-SNE to the grain samples data and inspect the
resulting t-SNE features using a scatter plot. You are given an array
`samples` of grain samples and a list `variety_numbers` giving the
variety number of each grain sample.

**Instructions**

- Import `TSNE` from `sklearn.manifold`.
- Create a TSNE instance called `model` with `learning_rate=200`.
- Apply the `.fit_transform()` method of `model` to `samples`. Assign
  the result to `tsne_features`.
- Select the column `0` of `tsne_features`. Assign the result to `xs`.
- Select the column `1` of `tsne_features`. Assign the result to `ys`.
- Make a scatter plot of the t-SNE features `xs` and `ys`. To color the
  points by the grain variety, specify the additional keyword argument
  `c=variety_numbers`.

**Answer**

```{python}
# Import TSNE
from sklearn.manifold import TSNE

# Create a TSNE instance: model
model = TSNE(learning_rate=200)

# Apply fit_transform to samples: tsne_features
tsne_features = model.fit_transform(samples)

# Select the 0th feature: xs
xs = tsne_features[:,0]

# Select the 1st feature: ys
ys = tsne_features[:,1]

# Scatter plot, coloring by variety_numbers
plt.scatter(xs, ys, c=variety_numbers)
plt.show()
```

### A t-SNE map of the stock market

t-SNE provides great visualizations when the individual samples can be
labeled. In this exercise, you'll apply t-SNE to the company stock price
data. A scatter plot of the resulting t-SNE features, labeled by the
company names, gives you a map of the stock market! The stock price
movements for each company are available as the array
`normalized_movements` (these have already been normalized for you). The
list `companies` gives the name of each company. PyPlot (`plt`) has been
imported for you.

**Instructions**

- Import `TSNE` from `sklearn.manifold`.
- Create a TSNE instance called `model` with `learning_rate=50`.
- Apply the `.fit_transform()` method of `model` to
  `normalized_movements`. Assign the result to `tsne_features`.
- Select column `0` and column `1` of `tsne_features`.
- Make a scatter plot of the t-SNE features `xs` and `ys`. Specify the
  additional keyword argument `alpha=0.5`.
- Code to label each point with its company name has been written for
  you using `plt.annotate()`, so just hit submit to see the
  visualization!

**Answer**

```{python}
# Import TSNE
from sklearn.manifold import TSNE

# Create a TSNE instance: model
model = TSNE(learning_rate=50)

# Apply fit_transform to normalized_movements: tsne_features
tsne_features = model.fit_transform(normalized_movements)

# Select the 0th feature: xs
xs = tsne_features[:,0]

# Select the 1th feature: ys
ys = tsne_features[:,1]

# Scatter plot
plt.scatter(xs, ys, alpha=0.5)

# Annotate the points
for x, y, company in zip(xs, ys, companies):
    plt.annotate(company, (x, y), fontsize=5, alpha=0.75)
plt.show()
```

## Decorrelating Your Data and Dimension Reduction

### Correlated data in nature

You are given an array `grains` giving the width and length of samples
of grain. You suspect that width and length will be correlated. To
confirm this, make a scatter plot of width vs length and measure their
Pearson correlation.

**Instructions**

- Import:
  - `matplotlib.pyplot` as `plt`.
  - `pearsonr` from `scipy.stats`.
- Assign column `0` of `grains` to `width` and column `1` of `grains` to
  `length`.
- Make a scatter plot with `width` on the x-axis and `length` on the
  y-axis.
- Use the `pearsonr()` function to calculate the Pearson correlation of
  `width` and `length`.

**Answer**

```{python}
# Perform the necessary imports
import matplotlib.pyplot as plt
from scipy.stats import pearsonr

# Assign the 0th column of grains: width
width = grains[:,0]

# Assign the 1st column of grains: length
length = grains[:,1]

# Scatter plot width vs length
plt.scatter(width, length)
plt.axis('equal')
plt.show()

# Calculate the Pearson correlation
correlation, pvalue = pearsonr(width, length)

# Display the correlation
print(correlation)

```

### Decorrelating the grain measurements with PCA

You observed in the previous exercise that the width and length
measurements of the grain are correlated. Now, you'll use PCA to
decorrelate these measurements, then plot the decorrelated points and
measure their Pearson correlation.

**Instructions**

- Import `PCA` from `sklearn.decomposition`.
- Create an instance of `PCA` called `model`.
- Use the `.fit_transform()` method of `model` to apply the PCA
  transformation to `grains`. Assign the result to `pca_features`.
- The subsequent code to extract, plot, and compute the Pearson
  correlation of the first two columns `pca_features` has been written
  for you, so hit submit to see the result!

**Answer**

```{python}
# Import PCA
from sklearn.decomposition import PCA

# Create PCA instance: model
model = PCA()

# Apply the fit_transform method of model to grains: pca_features
pca_features = model.fit_transform(grains)

# Assign 0th column of pca_features: xs
xs = pca_features[:,0]

# Assign 1st column of pca_features: ys
ys = pca_features[:,1]

# Scatter plot xs vs ys
plt.scatter(xs, ys)
plt.axis('equal')
plt.show()

# Calculate the Pearson correlation of xs and ys
correlation, pvalue = pearsonr(xs, ys)

# Display the correlation
print(correlation)
```

### The first principal component

The first principal component of the data is the direction in which the
data varies the most. In this exercise, your job is to use PCA to find
the first principal component of the length and width measurements of
the grain samples, and represent it as an arrow on the scatter plot.

The array `grains` gives the length and width of the grain samples.
PyPlot (`plt`) and `PCA` have already been imported for you.

**Instructions**

- Make a scatter plot of the grain measurements. This has been done for
  you.
- Create a `PCA` instance called `model`.
- Fit the model to the `grains` data.
- Extract the coordinates of the mean of the data using the `.mean_`
  attribute of `model`.
- Get the first principal component of `model` using the
  `.components_[0,:]` attribute.
- Plot the first principal component as an arrow on the scatter plot,
  using the `plt.arrow()` function. You have to specify the first two
  arguments - `mean[0]` and `mean[1]`.

**Answer**

```{python}
# Make a scatter plot of the untransformed points
plt.scatter(grains[:,0], grains[:,1])

# Create a PCA instance: model
model = PCA()

# Fit model to points
model.fit(grains)

# Get the mean of the grain samples: mean
mean = model.mean_

# Get the first principal component: first_pc
first_pc = model.components_[0,:]

# Plot first_pc as an arrow, starting at mean
plt.arrow(mean[0], mean[1], first_pc[0], first_pc[1], color='red', width=0.01)

# Keep axes on same scale
plt.axis('equal')
plt.show()
```

### Variance of the PCA features

The fish dataset is 6-dimensional. But what is its *intrinsic*
dimension? Make a plot of the variances of the PCA features to find out.
As before, `samples` is a 2D array, where each row represents a fish.
You'll need to standardize the features first.

**Instructions**

- Create an instance of `StandardScaler` called `scaler`.
- Create a `PCA` instance called `pca`.
- Use the `make_pipeline()` function to create a pipeline chaining
  `scaler` and `pca`.
- Use the `.fit()` method of `pipeline` to fit it to the fish samples
  `samples`.
- Extract the number of components used using the `.n_components_`
  attribute of `pca`. Place this inside a `range()` function and store
  the result as `features`.
- Use the `plt.bar()` function to plot the explained variances, with
  `features` on the x-axis and `pca.explained_variance_` on the y-axis.

**Answer**

```{python}
# Perform the necessary imports
from sklearn.decomposition import PCA
from sklearn.preprocessing import StandardScaler
from sklearn.pipeline import make_pipeline
import matplotlib.pyplot as plt

# Create scaler: scaler
scaler = StandardScaler()

# Create a PCA instance: pca
pca = PCA()

# Create pipeline: pipeline
pipeline = make_pipeline(scaler, pca)

# Fit the pipeline to 'samples'
pipeline.fit(samples)

# Plot the explained variances
features = range(pca.n_components_)
plt.bar(features, pca.explained_variance_)
plt.xlabel('PCA feature')
plt.ylabel('variance')
plt.xticks(features)
plt.show()
```

### Dimension reduction of the fish measurements

In a previous exercise, you saw that `2` was a reasonable choice for the
"intrinsic dimension" of the fish measurements. Now use PCA for
dimensionality reduction of the fish measurements, retaining only the 2
most important components.

The fish measurements have already been scaled for you, and are
available as `scaled_samples`.

**Instructions**

- Import `PCA` from `sklearn.decomposition`.
- Create a PCA instance called `pca` with `n_components=2`.
- Use the `.fit()` method of `pca` to fit it to the scaled fish
  measurements `scaled_samples`.
- Use the `.transform()` method of `pca` to transform the
  `scaled_samples`. Assign the result to `pca_features`.

**Answer**

```{python}
# Import PCA
from sklearn.decomposition import PCA

# Create a PCA instance with 2 components: pca
pca = PCA(n_components=2)

# Fit the PCA instance to the scaled samples
pca.fit(scaled_samples)

# Transform the scaled samples: pca_features
pca_features = pca.transform(scaled_samples)

# Print the shape of pca_features
print(pca_features.shape)
```

### A tf-idf word-frequency array

In this exercise, you'll create a tf-idf word frequency array for a toy
collection of documents. For this, use the `TfidfVectorizer` from
sklearn. It transforms a list of documents into a word frequency array,
which it outputs as a csr_matrix. It has `fit()` and `transform()`
methods like other sklearn objects.

You are given a list `documents` of toy documents about pets. Its
contents have been printed in the IPython Shell.

**Instructions**

- Import `TfidfVectorizer` from `sklearn.feature_extraction.text`.
- Create a `TfidfVectorizer` instance called `tfidf`.
- Apply `.fit_transform()` method of `tfidf` to `documents` and assign
  the result to `csr_mat`. This is a word-frequency array in csr_matrix
  format.
- Inspect `csr_mat` by calling its `.toarray()` method and printing the
  result. This has been done for you.
- The columns of the array correspond to words. Get the list of words by
  calling the `.get_feature_names()` method of `tfidf`, and assign the
  result to `words`.

**Answer**

```{python}
# Import TfidfVectorizer
from sklearn.feature_extraction.text import TfidfVectorizer

# Create a TfidfVectorizer: tfidf
tfidf = TfidfVectorizer() 

# Apply fit_transform to document: csr_mat
csr_mat = tfidf.fit_transform(documents)

# Print result of toarray() method
print(csr_mat.toarray())

# Get the words: words
words = tfidf.get_feature_names()

# Print words
print(words)
```

### Clustering Wikipedia part I

You saw in the video that `TruncatedSVD` is able to perform PCA on
sparse arrays in csr_matrix format, such as word-frequency arrays.
Combine your knowledge of TruncatedSVD and k-means to cluster some
popular pages from Wikipedia. In this exercise, build the pipeline. In
the next exercise, you'll apply it to the word-frequency array of some
Wikipedia articles.

Create a Pipeline object consisting of a TruncatedSVD followed by
KMeans. (This time, we've precomputed the word-frequency matrix for you,
so there's no need for a TfidfVectorizer).

The Wikipedia dataset you will be working with was obtained from
[here](https://blog.lateral.io/2015/06/the-unknown-perils-of-mining-wikipedia/).

**Instructions**

- Import:
  - `TruncatedSVD` from `sklearn.decomposition`.
  - `KMeans` from `sklearn.cluster`.
  - `make_pipeline` from `sklearn.pipeline`.
- Create a `TruncatedSVD` instance called `svd` with `n_components=50`.
- Create a `KMeans` instance called `kmeans` with `n_clusters=6`.
- Create a pipeline called `pipeline` consisting of `svd` and `kmeans`.

**Answer**

```{python}
# Perform the necessary imports
from sklearn.decomposition import TruncatedSVD
from sklearn.cluster import KMeans
from sklearn.pipeline import make_pipeline

# Create a TruncatedSVD instance: svd
svd = TruncatedSVD(n_components=50)

# Create a KMeans instance: kmeans
kmeans = KMeans(n_clusters=6)

# Create a pipeline: pipeline
pipeline = make_pipeline(svd, kmeans)
```

### Clustering Wikipedia part II

It is now time to put your pipeline from the previous exercise to work!
You are given an array `articles` of tf-idf word-frequencies of some
popular Wikipedia articles, and a list `titles` of their titles. Use
your pipeline to cluster the Wikipedia articles.

A solution to the previous exercise has been pre-loaded for you, so a
Pipeline `pipeline` chaining TruncatedSVD with KMeans is available.

**Instructions**

- Import `pandas` as `pd`.
- Fit the pipeline to the word-frequency array `articles`.
- Predict the cluster labels.
- Align the cluster labels with the list `titles` of article titles by
  creating a DataFrame `df` with `labels` and `titles` as columns. This
  has been done for you.
- Use the `.sort_values()` method of `df` to sort the DataFrame by the
  `'label'` column, and print the result.
- Hit submit and take a moment to investigate your amazing clustering of
  Wikipedia pages!

**Answer**

```{python}
# Import pandas
import pandas as pd

# Fit the pipeline to articles
pipeline.fit(articles)

# Calculate the cluster labels: labels
labels = pipeline.predict(articles)

# Create a DataFrame aligning labels and titles: df
df = pd.DataFrame({'label': labels, 'article': titles})

# Display df sorted by cluster label
print(df.sort_values('label'))
```

## Discovering Interpretable Features

### NMF applied to Wikipedia articles

In the video, you saw NMF applied to transform a toy word-frequency
array. Now it's your turn to apply NMF, this time using the tf-idf
word-frequency array of Wikipedia articles, given as a csr matrix
`articles`. Here, fit the model and transform the articles. In the next
exercise, you'll explore the result.

**Instructions**

- Import `NMF` from `sklearn.decomposition`.
- Create an `NMF` instance called `model` with `6` components.
- Fit the model to the word count data `articles`.
- Use the `.transform()` method of `model` to transform `articles`, and
  assign the result to `nmf_features`.
- Print `nmf_features` to get a first idea what it looks like
  (`.round(2)` rounds the entries to 2 decimal places.)

**Answer**

```{python}
# Import NMF
from sklearn.decomposition import NMF

# Create an NMF instance: model
model = NMF(n_components=6)

# Fit the model to articles
model.fit(articles)

# Transform the articles: nmf_features
nmf_features = model.transform(articles)

# Print the NMF features
print(nmf_features.round(2))
```

### NMF features of the Wikipedia articles

Now you will explore the NMF features you created in the previous
exercise. A solution to the previous exercise has been pre-loaded, so
the array `nmf_features` is available. Also available is a list `titles`
giving the title of each Wikipedia article.

When investigating the features, notice that for both actors, the NMF
feature 3 has by far the highest value. This means that both articles
are reconstructed using mainly the 3rd NMF component. In the next video,
you'll see why: NMF components represent topics (for instance, acting!).

**Instructions**

- Import `pandas` as `pd`.
- Create a DataFrame `df` from `nmf_features` using `pd.DataFrame()`.
  Set the index to `titles` using `index=titles`.
- Use the `.loc[]` accessor of `df` to select the row with title
  `'Anne Hathaway'`, and print the result. These are the NMF features
  for the article about the actress Anne Hathaway.
- Repeat the last step for `'Denzel Washington'` (another actor).

**Answer**

```{python}
# Import pandas
import pandas as pd

# Create a pandas DataFrame: df
df = pd.DataFrame(nmf_features, index=titles)

# Print the row for 'Anne Hathaway'
print(df.loc['Anne Hathaway'])

# Print the row for 'Denzel Washington'
print(df.loc['Denzel Washington'])
```

### NMF learns topics of documents

In the video, you learned when NMF is applied to documents, the
components correspond to topics of documents, and the NMF features
reconstruct the documents from the topics. Verify this for yourself for
the NMF model that you built earlier using the Wikipedia articles.
Previously, you saw that the 3rd NMF feature value was high for the
articles about actors Anne Hathaway and Denzel Washington. In this
exercise, identify the topic of the corresponding NMF component.

The NMF model you built earlier is available as `model`, while `words`
is a list of the words that label the columns of the word-frequency
array.

After you are done, take a moment to recognize the topic that the
articles about Anne Hathaway and Denzel Washington have in common!

**Instructions**

- Import `pandas` as `pd`.
- Create a DataFrame `components_df` from `model.components_`, setting
  `columns=words` so that columns are labeled by the words.
- Print `components_df.shape` to check the dimensions of the DataFrame.
- Use the `.iloc[]` accessor on the DataFrame `components_df` to select
  row `3`. Assign the result to `component`.
- Call the `.nlargest()` method of `component`, and print the result.
  This gives the five words with the highest values for that component.

**Answer**

```{python}
# Import pandas
import pandas as pd

# Create a DataFrame: components_df
components_df = pd.DataFrame(model.components_, columns=words)

# Print the shape of the DataFrame
print(components_df.shape)

# Select row 3: component
component = components_df.iloc[3]

# Print result of nlargest
print(component.nlargest())
```

### Explore the LED digits dataset

In the following exercises, you'll use NMF to decompose grayscale images
into their commonly occurring patterns. Firstly, explore the image
dataset and see how it is encoded as an array. You are given 100 images
as a 2D array `samples`, where each row represents a single 13x8 image.
The images in your dataset are pictures of a LED digital display.

**Instructions**

- Import `matplotlib.pyplot` as `plt`.
- Select row `0` of `samples` and assign the result to `digit`. For
  example, to select column `2` of an array `a`, you could use `a[:,2]`.
  Remember that since `samples` is a NumPy array, you can't use the
  `.loc[]` or `iloc[]` accessors to select specific rows or columns.
- Print `digit`. This has been done for you. Notice that it is a 1D
  array of 0s and 1s.
- Use the `.reshape()` method of `digit` to get a 2D array with shape
  `(13, 8)`. Assign the result to `bitmap`.
- Print `bitmap`, and notice that the 1s show the digit 7!
- Use the `plt.imshow()` function to display `bitmap` as an image.

**Answer**

```{python}
# Import pyplot
from matplotlib import pyplot as plt

# Select the 0th row: digit
digit = samples[0,:]

# Print digit
print(digit)

# Reshape digit to a 13x8 array: bitmap
bitmap = digit.reshape((13, 8))

# Print bitmap
print(bitmap)

# Use plt.imshow to display bitmap
plt.imshow(bitmap, cmap='gray', interpolation='nearest')
plt.colorbar()
plt.show()
```

### NMF learns the parts of images

Now use what you've learned about NMF to decompose the digits dataset.
You are again given the digit images as a 2D array `samples`. This time,
you are also provided with a function `show_as_image()` that displays
the image encoded by any 1D array:

    def show_as_image(sample):
        bitmap = sample.reshape((13, 8))
        plt.figure()
        plt.imshow(bitmap, cmap='gray', interpolation='nearest')
        plt.colorbar()
        plt.show()

After you are done, take a moment to look through the plots and notice
how NMF has expressed the digit as a sum of the components!

**Instructions**

- Import `NMF` from `sklearn.decomposition`.
- Create an `NMF` instance called `model` with `7` components. (7 is the
  number of cells in an LED display).
- Apply the `.fit_transform()` method of `model` to `samples`. Assign
  the result to `features`.
- To each component of the model (accessed via `model.components_`),
  apply the `show_as_image()` function to that component inside the
  loop.
- Assign the row `0` of `features` to `digit_features`.
- Print `digit_features`.

**Answer**

```{python}
# Import NMF
from sklearn.decomposition import NMF

# Create an NMF model: model
model = NMF(n_components=7)

# Apply fit_transform to samples: features
features = model.fit_transform(samples)

# Call show_as_image on each component
for component in model.components_:
    show_as_image(component)

# Select the 0th row of features: digit_features
digit_features = features[0,:]

# Print digit_features
print(digit_features)
```

### PCA doesn't learn parts

Unlike NMF, PCA *doesn't* learn the parts of things. Its components do
not correspond to topics (in the case of documents) or to parts of
images, when trained on images. Verify this for yourself by inspecting
the components of a PCA model fit to the dataset of LED digit images
from the previous exercise. The images are available as a 2D array
`samples`. Also available is a modified version of the `show_as_image()`
function which colors a pixel red if the value is negative.

After submitting the answer, notice that the components of PCA do not
represent meaningful parts of images of LED digits!

**Instructions**

- Import `PCA` from `sklearn.decomposition`.
- Create a `PCA` instance called `model` with `7` components.
- Apply the `.fit_transform()` method of `model` to `samples`. Assign
  the result to `features`.
- To each component of the model (accessed via `model.components_`),
  apply the `show_as_image()` function to that component inside the
  loop.

**Answer**

```{python}
# Import PCA
from sklearn.decomposition import PCA

# Create a PCA instance: model
model = PCA(n_components=7)

# Apply fit_transform to samples: features
features = model.fit_transform(samples)

# Call show_as_image on each component
for component in model.components_:
    show_as_image(component)
```

### Which articles are similar to 'Cristiano Ronaldo'?

In the video, you learned how to use NMF features and the cosine
similarity to find similar articles. Apply this to your NMF model for
popular Wikipedia articles, by finding the articles most similar to the
article about the footballer Cristiano Ronaldo. The NMF features you
obtained earlier are available as `nmf_features`, while `titles` is a
list of the article titles.

**Instructions**

- Import `normalize` from `sklearn.preprocessing`.
- Apply the `normalize()` function to `nmf_features`. Store the result
  as `norm_features`.
- Create a DataFrame `df` from `norm_features`, using `titles` as an
  index.
- Use the `.loc[]` accessor of `df` to select the row of
  `'Cristiano Ronaldo'`. Assign the result to `article`.
- Apply the `.dot()` method of `df` to `article` to calculate the cosine
  similarity of every row with `article`.
- Print the result of the `.nlargest()` method of `similarities` to
  display the most similar articles. This has been done for you, so hit
  'Submit Answer' to see the result!

**Answer**

```{python}
# Perform the necessary imports
import pandas as pd
from sklearn.preprocessing import normalize

# Normalize the NMF features: norm_features
norm_features = normalize(nmf_features)

# Create a DataFrame: df
df = pd.DataFrame(norm_features, index=titles)

# Select the row corresponding to 'Cristiano Ronaldo': article
article = df.loc['Cristiano Ronaldo']

# Compute the dot products: similarities
similarities = df.dot(article)

# Display those with the largest cosine similarity
print(similarities.nlargest())
```

### Recommend musical artists part I

In this exercise and the next, you'll use what you've learned about NMF
to recommend popular music artists! You are given a sparse array
`artists` whose rows correspond to artists and whose columns correspond
to users. The entries give the number of times each artist was listened
to by each user.

In this exercise, build a pipeline and transform the array into
normalized NMF features. The first step in the pipeline, `MaxAbsScaler`,
transforms the data so that all users have the same influence on the
model, regardless of how many different artists they've listened to. In
the next exercise, you'll use the resulting normalized NMF features for
recommendation!

**Instructions**

- Import:
  - `NMF` from `sklearn.decomposition`.
  - `Normalizer` and `MaxAbsScaler` from `sklearn.preprocessing`.
  - `make_pipeline` from `sklearn.pipeline`.
- Create an instance of `MaxAbsScaler` called `scaler`.
- Create an `NMF` instance with `20` components called `nmf`.
- Create an instance of `Normalizer` called `normalizer`.
- Create a pipeline called `pipeline` that chains together `scaler`,
  `nmf`, and `normalizer`.
- Apply the `.fit_transform()` method of `pipeline` to `artists`. Assign
  the result to `norm_features`.

**Answer**

```{python}
# Perform the necessary imports
from sklearn.decomposition import NMF
from sklearn.preprocessing import Normalizer, MaxAbsScaler
from sklearn.pipeline import make_pipeline

# Create a MaxAbsScaler: scaler
scaler = MaxAbsScaler()

# Create an NMF model: nmf
nmf = NMF(n_components=20)

# Create a Normalizer: normalizer
normalizer = Normalizer()

# Create a pipeline: pipeline
pipeline = make_pipeline(scaler, nmf, normalizer)

# Apply fit_transform to artists: norm_features
norm_features = pipeline.fit_transform(artists)
```

### Recommend musical artists part II

Suppose you were a big fan of Bruce Springsteen - which other musical
artists might you like? Use your NMF features from the previous exercise
and the cosine similarity to find similar musical artists. A solution to
the previous exercise has been run, so `norm_features` is an array
containing the normalized NMF features as rows. The names of the musical
artists are available as the list `artist_names`.

**Instructions**

- Import `pandas` as `pd`.
- Create a DataFrame `df` from `norm_features`, using `artist_names` as
  an index.
- Use the `.loc[]` accessor of `df` to select the row of
  `'Bruce Springsteen'`. Assign the result to `artist`.
- Apply the `.dot()` method of `df` to `artist` to calculate the dot
  product of every row with `artist`. Save the result as `similarities`.
- Print the result of the `.nlargest()` method of `similarities` to
  display the artists most similar to `'Bruce Springsteen'`.

**Answer**

```{python}
# Import pandas
import pandas as pd

# Create a DataFrame: df
df = pd.DataFrame(norm_features, index=artist_names)

# Select row of 'Bruce Springsteen': artist
artist = df.loc['Bruce Springsteen']

# Compute cosine similarities: similarities
similarities = df.dot(artist)

# Display those with highest cosine similarity
print(similarities.nlargest())
```
