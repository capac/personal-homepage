---
layout: post
title: Bicycle Use Visualization With Bokeh
featured: true
image: /assets/bicycle-use-visualization-with-bokeh/bike_traffic.png
caption: Average weekday public bicycle traffic in London UK at 17:45 pm. The large blue and purple dots show the inflow to King's Cross and Waterloo train stations, while the light green and orange dots show the outflows from central London. There are also sizable, light green dots for dock stations in Hyde Park and Kensington Gardens and Queen Elizabeth Olympic Park. 
tags: london, bicycles, bokeh
comments: true
---

I've dedicated some time to build an interactive scatter plot with [Bokeh](https://docs.bokeh.org/en/latest/) to show the average weekday bicycle traffic in London UK for 2019. The interactive plot displays the total and net traffic fluxes for 748 docking stations and 12940 bicycles in downtown London. The size of each data point corresponds to the total traffic flux given by the sum of the mean inflow and outflow, and the color shows the net flux given by the difference between the mean inflow and outflow. The blue-shifted colors represent a net inflow of bicycles while the red-shifted colors represent a net outflow.

The interactive plot can be animated by a play button which displays the activty over a 24 hour period, in fifteen minutes increments. It can be zoomed in and filtered by total traffic flux and contains tooltip information which can be accessed by cursor hover-over on each docking station, showing the details concerning total traffic, net traffic and docking station name.

One can notice the outflow of bicycles from the King's Cross and Waterloo train stations during the morning rush hours around 8 am and the corresponding inflow to the same train stations during the evening rush hours around 6 pm. To the outflow from the train stations corresponds an inflow to the various docking stations in central London. You can also notice an increase in bicycle traffic at the docking stations in Hyde Park, Kensington Gardens and Queen Elizabeth Olympic Park during the afternoon hours.

The data is freely available and can be downloaded from [the public open data web site for TfL](https://cycling.data.tfl.gov.uk/). The code for the Bokeh visualization is on [my GitHub repository](https://github.com/capac/bicycle-use-visualization-with-bokeh). The actual visualization is hosted at the link below. Check it out, and tell me what you think.

[Data visualization for weekday public bicycle use in London UK using Bokeh](https://bicycle-use-with-bokeh.herokuapp.com/main)
