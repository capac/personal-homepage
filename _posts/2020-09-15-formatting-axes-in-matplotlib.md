---
layout: post
title: Formatting data for matplotlib axes
draft: True
featured: False
image: /assets/formatting-axes-in-matplotlib/pexels-stein-egil-liland-3855630.jpg
image-url: https://www.pexels.com/photo/silhouette-of-a-person-on-a-swing-3293148/
image-source: Pexels
image-author: Stein Egil Liland
tags: ['matplotlib']
comments: False
---

A simple but useful feature for data visualization in plots is to format the axes accoring to the units of the attributes you are working with, which could just be metric units, currency units or similar. Matplotlib makes this fairly easy actually, without having to modify your data, by importing the [**matplotlib.ticker**](https://matplotlib.org/stable/api/ticker_api.html?highlight=ticker#) class. 

```python
from matplotlib import ticker
import matplotlib.pyplot as plt
fig, axes = plt.subplots()
...
scale = 1e6  # or any other scaling factor
ticks = ticker.FuncFormatter(lambda x, pos: '{0:g}'.format(x/scale))
axes.yaxis.set_major_formatter(ticks)
```

The issue you need to watch out for is that the _lambda_ function in the example above takes two arguments, because the [**matplotlib.ticker.FuncFormatter**](https://matplotlib.org/stable/api/ticker_api.html?highlight=ticker#matplotlib.ticker.FuncFormatter) _"take[s] two inputs (a tick value x and a position pos), and return[s] a string containing the corresponding tick label"_, as described in the documents.

In a similar manner instead of using a _lambda_ function, I also made a short function to add a prefix for numbers that are over a million, here specifically for dollar amounts, which is then fed to the same _matplotlib.ticker.FuncFormatter_ class. 

```python
import matplotlib.pyplot as plt
from matplotlib.ticker import FuncFormatter
fig, axes = plt.subplots()

# format the currency
def currency(x, pos):
    # The two args are the value and tick position
    if x >= 1000000:
        return '${:1.1f}M'.format(x*1e-6)
    return '${:1.0f}K'.format(x*1e-3)

formatter = FuncFormatter(currency)
axes.xaxis.set_major_formatter(formatter)
```

<!-- # Figure or image without caption
![Plot](/assets/...)

# Figure or image with caption
{% include image.html
    src="/assets/..."
    alt="Image title"
    caption="Image caption"
%} -->
