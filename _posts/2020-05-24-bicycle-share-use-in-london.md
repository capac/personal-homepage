---
layout: post
title: Bicycle Share Use in London
featured: true
image: /assets/bicycle-share-use-in-london/ben-lodge-hmKirr0dnaM-unsplash.jpg
image-url: https://unsplash.com/photos/hmKirr0dnaM
image-source: Unsplash
image-author: Ben Lodge
tags: london, bicycles
---

The public bicycle sharing scheme has been in use in London for just over ten years now, since it was first introduced by the then mayor of London Boris Johnson in July 2010. It has had a slow but steady increase in use and popularity over the course of the years, such that there is [a plan to further increase the number of dock stations and bicycles currently in use](https://tfl.gov.uk/info-for/media/press-releases/2020/june/tfl-to-expand-santander-cycles-scheme-to-keep-up-with-demand) to cover a larger area of the downtown and nearby boroughs of the city. The bicycle usage by London city residents is most definitely an interesting project in data analysis and visualization in itself, given particularly that [the data is freely available for public consumption on the TfL web site](https://cycling.data.tfl.gov.uk/).

A first approach to the data analysis can be taken with the plot of the total number of bicycle rides per day over 2019. It is more than apparent the rise in bicycle use from the colder months of winter to the warmer season. Some of the more extreme outliers in the plot come unsurprising from days with good and bad weather and also from the holidays.

![Total number of rides per day for 2019](/assets/bicycle-share-use-in-london/tot_num_rides_per_day_2019.png)

The total number of rides per month in 2019 can be observed in the plot below. Here too one notices the rise in bicycle use from the cold months of winter to summer, and you can also notice an increase in bicycle use over the course of the weekends following the same trend from winter to summer and back. The peak of total bicycle use occurs in July, although June shows a greater use in bicycle rides on weekends.

![Total number of rides by month in 2019](/assets/bicycle-share-use-in-london/tot_rides_by_month_2019.png "Total number of rides by month in 2019")

This last observation becomes more apparent in the following plot. The ratio of weekday to weekend rides in 2019 shows the peak demand for bicycles during the weekends of June. In general one can notice a slow uptick in weekend demand during the summer months compared to the demand in the colder season.

![Ratio of the number of rides per month for 2019](/assets/bicycle-share-use-in-london/ratio_rides_by_month_2019.png)

The plot below shows the effect of bicycle hires during weekdays, where there are two peaks occurring during the morning rush hour around eight o'clock and during the evening rush hour around six o'clock. Moderate bicycle use can be seen between the two peaks, while the lowest lull in bicycle use occurs naturally over the night hours.

![Average number of rides per hour on weekdays in 2019](/assets/bicycle-share-use-in-london/avg_num_rides_hour_weekdays_2019.png)

A different behavior can be observed during the weekends, where there is a slow increase in bicycle use from the morning until about the mid afternoon around two to three o'clock with a slower decrease afterwards.

![Average number of rides per hour on weekends in 2019](/assets/bicycle-share-use-in-london/avg_num_rides_hour_weekends_2019.png)

For the data manipulation I used _SQLite_, _pandas_ and _Python_, while for the plots I used _matplotlib_. The [cycling data are available as CSV files](https://cycling.data.tfl.gov.uk/) directly from Tfl, as part of the TfL policy for [open data users](https://tfl.gov.uk/info-for/open-data-users/).
