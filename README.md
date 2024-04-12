# ETA-Accuracy-Benchmark
A common methodology for measuring the accuracy of real-time ETA predictions

## Why does the ETA Accuracy Benchmark exist?

Accurate real-time predictions are among the most important things to transit riders. They’re closely tied to rider satisfaction, perceived reliability, and ridership as a whole. Unfortunately, there has never been a consistent, industry-wide definition for what an “accurate prediction” actually means.

The ETA Accuracy Benchmark aims to solve this problem. It is based on a methodology initially developed by IBI Group, which is unique for incorporating **time buckets** and **asymmetric thresholds** to calculate prediction accuracy. This methodology aims to capture how riders actually experience public transit waiting times.

- **Time buckets.** We know that riders perceive prediction accuracy more strictly the closer the transit vehicle is to arriving. To reflect this, the ETA Accuracy Benchmark definition has stricter thresholds the closer a vehicle is to arriving and more forgiving thresholds for arrivals that are still far away.
- **Asymmetric thresholds.** We also know that _early arrivals_ are worse for riders than _late arrivals_. When a transit vehicle arrives earlier than predicted, many riders will miss the trip despite getting to the stop when the arrival is predicted. When a vehicle is late, riders may have to wait at the stop a little longer but they will not miss the trip altogether. To reflect this, the ETA Accuracy Benchmark definition penalizes early departures more harshly than late departures.

## Overview

The ETA Accuracy Benchmark uses the following methodology for analyzing prediction accuracy:

1. Creating a sample of predictions and actual arrivals
2. Categorizing each prediction in the sample as “accurate” or “inaccurate” according to where it falls within the permitted accuracy thresholds
3. Bucketing each prediction into one of four time buckets depending on how far away the vehicle was  when the prediction was sampled
4. Calculating the accuracy percentage of each bucket by comparing the total number of “accurate” predictions to the overall number of predictions in each bucket
5. Calculating an overall prediction accuracy percentage by taking an equally weighted average of the four buckets

More details on how to complete each step are enumerated below.

## Step-by-step guide to analyzing prediction accuracy

**1. Creating a sample of predictions**
- Start with a set of predictions over a given time range and set of routes.  This can be created from GTFS-rt updates and can include either all predictions or be a representative sample.
- Create a dataset of actual arrivals (or departures) to compare predictions to.

**2. Categorizing each prediction in the sample as “accurate” or “inaccurate” according to where it falls within the permitted accuracy thresholds**

For each prediction in the analysis set, analyze its accuracy. Accuracy is determined by three things: 
- **Time variance:** Calculate the difference between _predicted arrival_ (or departure) and _actual arrival_ (or departure)
- **Time bucket:** Use ‘time to actual’ to determine the time bucket; i.e. a prediction goes into a bucket based on the amount of time between when the _prediction was sampled_ and when the vehicle _actually reached the stop_.
- **Accuracy value:** Compare the time variance to the accuracy threshold for the associated time bucket. Any prediction inside of its associated accuracy threshold is deemed “accurate.” Any prediction outside of its associated accuracy threshold is deemed “inaccurate.”

![240411_partner-transit_eta-accuracy-benchmark_in-line_technical-1_1920x1080](https://github.com/TransitApp/ETA-Accuracy-Benchmark/assets/32456691/a7c4ce21-2e77-4897-a8bc-2a7939a1eea1)

Note that:
- Time buckets exclude boundaries, i.e. `>= bucket start time < bucket end time`
- For accuracy thresholds, the _boundary is inclusive_: For example, for the 0-3 minute time bucket, the accuracy band is 30 seconds early to 90 seconds late; both a 30-seconds early and a 90-seconds late prediction are considered accurate.

**3. Bucketing each prediction into one of four time buckets depending on how far away the vehicle was when the prediction was sampled**

The ETA Accuracy Benchmark uses progressively narrower accuracy thresholds depending on how far the transit vehicle is from arriving to reflect the various stages of the waiting period. It’s like a dartboard that’s moving toward the rider as they wait at the stop, with a bullseye that gets smaller and smaller the closer it gets:

- **10-15 mins away.** When a transit vehicle is still far away, riders may still be weighing other modes. Should they drive? A prediction generated during this timeframe is considered accurate if the transit vehicle actually arrives no earlier than 1.5 minutes before and no later than 4.5 minutes after the predicted arrival time.
- **6-10 mins away.** Riders here are assessing how quickly they need to get out the door. Can they finish up what they’re doing, or do they need to drop everything and head to the stop? A prediction generated during this timeframe is considered accurate if the transit vehicle actually arrives no earlier than 1 minute before and no later than 3.5 minutes after the predicted arrival time.
- **3-6 mins away.** On average, people walk about a quarter-mile to a transit stop, which for most people takes about 5 minutes. When a vehicle is 3 to 6 minutes away, riders are weighing how quickly they need to cover that distance. Can they walk at a normal pace, or do they need to power walk? A prediction generated during this timeframe is considered accurate if the vehicle actually arrives no earlier than 1 minute before and no later than 2.5 minutes after the predicted arrival time.
- **0-3 mins away.** When the transit vehicle is about to arrive, riders just want to know where it is. Should they stand up and start looking for an approaching bus? A prediction generated during this timeframe is considered accurate when a vehicle actually arrives no earlier than 30 seconds before and no later than 1.5 minutes after the predicted arrival time.

**4. Calculating the accuracy percentage of each bucket by dividing the total number of “accurate” predictions by the total number of predictions in the bucket.**

Accuracy by bucket: Once all predictions have been bucketed appropriately, find the average prediction accuracy for a bucket by taking the `(total number of accurate predictions)`/`(total number of predictions)`.

![240411_partner-transit_eta-accuracy-benchmark_in-line_technical-2_1920x1080](https://github.com/TransitApp/ETA-Accuracy-Benchmark/assets/32456691/16f7f269-91c2-4f75-9ef5-81a4447b8253)

**5. Calculating an overall prediction accuracy percentage by taking an equally weighted average of the four buckets.**

Overall accuracy: in order to find an overall accuracy, take a straight average of the four accuracy buckets.

This gives the smaller (and more impactful) buckets more weight. There are fewer total predictions in the smaller buckets — only 3 minutes’ worth of predictions in the 0-3 minute bucket compared to 5 minutes’ worth of predictions in the 10-15 minute bucket. By taking a straight average of the bucket-level percentages, the benchmark effectively gives additional weight to the smaller buckets simply because there are fewer predictions in those buckets. This straight average is the “single-number” readout to evaluate a feed’s overall accuracy.

![240411_partner-transit_eta-accuracy-benchmark_in-line_technical-3_1920x1080](https://github.com/TransitApp/ETA-Accuracy-Benchmark/assets/32456691/3856a10a-def4-4043-9c4b-e9216d9fa13a)
