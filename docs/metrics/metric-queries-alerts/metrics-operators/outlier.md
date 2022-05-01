---
id: outlier
---

# outlier

This page describes the metrics `outlier` operator and how to use it in a metric query.

The metrics `outlier` operator identifies metrics data points that are outside the range of expected values. Outliers help you spot unusual behavior in your metrics visualizations and track the behavior over time.

The `outlier` operator tracks the moving average and standard deviation of a time series over a specified time window, and calculates a threshold band, outside of which data points are considered outliers. You can use optional qualifiers to specify the time window, the number of standard deviations beyond which a data point is considered an outlier, and the directionality of the deviation.

At this time, using the `outlier` operator in a metric monitor is not supported.

## outlier syntax

`metric query | outlier [window\<\>, threshold\<\>, direction=[ +- | + | - ]]`

Where:

* `window` is the time window, in minutes, over which to calculate the moving average and standard deviation of the time series. Default: 5m If you include the `window` qualifier, be sure to explicitly specify the units, for example `window=5m`.
* `threshold` is the number of standard deviations from the moving average that defines the threshold band. Default: 3
* `direction` specifies what deviation direction should trigger violations: positive deviations (+), negative deviations (-), or both (+-). Default: +-

![outlier-operator.png](/img/metrics/outlier-operator.png)

In the visualization, the threshold band is the part shaded in pink. The outlier values are represented by the pink triangles.