---
layout: post
title: Formatting data for matplotlib axes
draft: True
featured: False
image: /assets/...
image-author: Image author name
image-source: Image source description
image-url: https://...
tags: ['matplotlib']
comments: True / False
---

A simple but useful feature for data is to format the axes accoring to the units of the attributes you are working with, which could just be metric units or currency untis or something similar. Matplotlib make this fairly easy actually, by importing the **ticker** class. 

```python
...
from matplotlib import ticker
ticks = ticker.FuncFormatter(lambda x, pos: '{0:g}'.format(x/1e4))
axes.yaxis.set_major_formatter(ticks)
```

<!-- # Figure or image without caption
![Plot](/assets/...)

# Figure or image with caption
{% include image.html
    src="/assets/..."
    alt="Image title"
    caption="Image caption"
%} -->
