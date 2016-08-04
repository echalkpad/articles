# Accuracy in the Palm of Your Hand

[Original URL](http://gpsworld.com/accuracy-in-the-palm-of-your-hand/)

> Centimeter Positioning with a Smartphone-Quality GNSS Antenna By Kenneth M. Pesyna, Jr., Robert W. Heath, Jr. and Todd E. Humphreys, the University of Texas at Austin The smartphone antenna's...

## [<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna_opener-1024x682.jpg%201024w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna_opener-250x167.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna_opener-300x200.jpg%20300w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna_opener.jpg%201025w" class="alignnone wp-image-34214" width="739">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna_opener.jpg)

## <span class="s1">Centimeter Positioning with a Smartphone-Quality GNSS Antenna</span>

_By Kenneth M. Pesyna, Jr., Robert W. Heath, Jr. and Todd E. Humphreys, the University of Texas at Austin_

**The smartphone antenna's poor multipath suppression and irregular gain pattern result in large time-correlated phase errors that significantly increase the time to integer ambiguity resolution as compared to even a low-quality stand-alone patch antenna. The time to integer resolution -- and to a centimeter-accurate fix -- is significantly reduced when more GNSS signals are tracked or when the smartphone experiences gentle wavelength-scale random motion.**

GNSS chipsets are now ubiquitous in smartphones and tablets. Yet the underlying positioning accuracy of these consumer-grade GNSS receivers has stagnated over the past decade. The latest clock, orbit, and atmospheric models have improved ranging accuracy to a meter or so, leaving receiver-dependent multipath and front-end-noise-induced variations as the dominant sources of error in current consumer devices. Under good multipath conditions, 2-to-3-meter-accurate positioning is typical; under adverse multipath, accuracy degrades to 10 meters or worse.

<span class="s1">Yet outside the mainstream of consumer GNSS receivers, centimeter — even millimeter — accurate GNSS receivers can be found. These high-precision receivers are used routinely in geodesy, agriculture, and surveying. Their exquisite accuracy results from replacing standard code-phase positioning techniques with carrier phase differential GNSS (CDGNSS) techniques. Currently, the primary impediment to performing CDGNSS positioning on smartphones lies not in the commodity GNSS chipset, which actually outperforms survey-grade chipsets in some respects, but in the antenna, whose chief failing is its poor multipath suppression. Multipath, caused by direct signals reflecting off the ground and nearby objects, induces centimeter-level phase measurement errors, which, for static receivers, have decorrelation times of hundreds of seconds. The large size and strong time correlation of these errors significantly increases the initialization period — the so-called time-to-ambiguity-resolution (TAR) — of GNSS receivers employing CDGNSS to obtain centimeter-level positioning accuracy. </span>

<span class="s1">Prior work on centimeter-accurate positioning with low-cost mobile devices has focused on external devices, or “pucks,” which contain a GNSS antenna and chipset. These devices interface with the smartphone via Bluetooth or a wired connection. Such solutions, which enjoy the better sensitivity and multipath suppression offered by their comparatively large, high-quality GNSS antennas, do not provide insight into the feasibility of CDGNSS on a stand-alone smartphone platform. </span>

This article demonstrates that centimeter-accurate CDGNSS positioning is indeed possible based on data sampled from a smartphone-quality GNSS antenna. This result has far-reaching significance for precise mass-market positioning. We offer an empirical analysis of the average gain and carrier phase multipath error susceptibility of smartphone-grade GNSS antennas. We also demonstrate that, for low-quality GNSS antennas such as those in smartphones, wavelength-scale random antenna motion substantially improves the time to integer ambiguity resolution.

<span class="s1">This article focuses on single-frequency CDGNSS rather than multiple-frequency CDGNSS or other carrier-phase-based techniques, such as precise-point positioning (PPP), for three reasons. First, virtually all smartphones are equipped with single-frequency GNSS antennas tuned to the L1 band centered at 1575.42 MHz, and single-frequency CDGNSS will likely forever remain the cheapest option. Second, as compared to PPP, CDGNSS converges much faster to centimeter accuracy, which will be important for impatient smartphone users. </span>

<span class="s1">Finally, as centimeter-accurate GNSS moves into the mass market, GNSS reference stations will proliferate so that the vast majority of users can expect to be within a few kilometers of one. In this so-called short baseline regime, the differential ionospheric delay between the reference and mobile receivers becomes insignificant, obviating differential delay estimation via multi-frequency measurements. Of course, the additional signal measurements produced by multiple-frequency receivers would lead to faster convergence times and improved robustness, but for many applications, single-frequency measurements will be adequate. </span>

**Test Architecture**

We used the test architecture shown in 

<span class="s2">
  <strong>Figure 1</strong>
</span>

to collect data from a smartphone-grade antenna and higher quality antennas, process these data through a software-defined GNSS receiver, and compute a CDGNSS solution on the basis of the carrier phase measurements output by the GNSS receiver.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-1.jpg%20750w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-1-250x159.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-1-300x191.jpg%20300w" class="size-full wp-image-34211" width="750">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-1.jpg)

 Figure 1\. Test architecture designed for an in-situ study of a smartphone-grade GNSS antenna. The analog GNSS signal is tapped off after the phone's internal bandpass filter and low-noise amplifier and is directed to a dedicated RF front-end for downconversion and digitization. Data are stored to file for subsequent post-processing by a software GNSS receiver and CDGNSS filter.

<span class="s1">The architecture has been designed such that the antenna is left undisturbed within the phone; data are collected by tapping off the analog signal immediately after the phone’s internal bandpass filter and low-noise amplifier. This analog signal is directed to an external radio frequency (RF) front-end and GNSS receiver. Use of an external receiver permits well-defined GNSS signal processing unencumbered by the limitations of the phone’s internal chipset and clock. </span>

The clock attached to the external front-end was an oven-controlled crystal oscillator (OCXO), which has much greater stability than the low-cost oscillators used to drive GNSS signal sampling within smartphones. However, it was found that reliable cycle-slip-free GNSS carrier tracking only required a 40-ms coherent integration (pre-detection) interval, which is within the coherence time of a low-cost temperature-compensated crystal oscillator (TCXO) at the GPS L1 frequency.

Although only a single model of smartphone was tested using this architecture -- a popular mass-market phone -- the results are assumed representative of all smartphones from the same manufacturer.

Using this architecture, many hours of raw high-rate (

<span class="s3">∼</span>

6 MHz) digitized intermediate frequency samples were collected and stored to disk for post processing. Also stored to disk were high-rate data from a survey-grade antenna, which served as the reference antenna for CDGNSS processing. An in-house software-defined GNSS receiver, known as GRID, was used to generate, from these samples, high-quality carrier phase measurements. GRID is a flexible receiver that can be easily adapted to maintain carrier lock despite severe fading. Complex baseband accumulations output from GRID allowed detailed analysis of the signal and tracking loop behavior to ensure that no cycle slips occurred. The generated carrier phase measurements were subsequently passed to a CDGNSS filter, a model for which is described in the next section.

**CDGNSS Processing**

The CDGNSS filter described in this section ingests double-differenced carrier phase measurements output from GRID and processes them to produce (1) the centimeter-accurate trajectory estimate of the mobile antenna, (2) a time history of phase residuals, (3) carrier phase integer ambiguity estimates, (4) theoretical integer ambiguity resolution success bounds, and (5) empirical integer ambiguity resolution success rates. These outputs are used to analyze the performance of the smartphone-grade antenna and compare its performance to higher-quality antennas.

<span class="s4">
  <strong>CDGNSS Filter Model.</strong>
</span>

The filter's state has a real-valued component _x

<sub>k</sub>_

 that models the mobile antenna's relative center of motion, its instantaneous offset from this center of motion, and its velocity at each time epoch _k_:

[![Eq_1](http://gpsworld.com/wp-content/uploads/2015/02/Eq_1.jpg)](http://gpsworld.com/wp-content/uploads/2015/02/Eq_1.jpg). (1)

The filter's state also has an integer-valued component that models the CDGNSS phase ambiguities:

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Eq_2.jpg%20288w,%20http://gpsworld.com/wp-content/uploads/2015/02/Eq_2-250x23.jpg%20250w" class="alignnone wp-image-34197" width="277">](http://gpsworld.com/wp-content/uploads/2015/02/Eq_2.jpg)

(2)

where _N

<sub>SV</sub>_

 is the total number of satellites tracked. Such integer ambiguities are inherent to carrier phase differential positioning techniques; their resolution has been the topic of much past research and is required to produce a CDGNSS positioning solution.

<span class="s4">
  <strong>Dynamics and Measurement Models.</strong>
</span>

The real-valued state component _x

<sub>k</sub>_

 is assumed to evolve as a mean-reverting second-order Gauss-Markov process. This process models the time-correlated and mean-reverting motion a smartphone experiences when held or moved gently in the extended hand of an otherwise stationary user. The integer-valued state component _n

<sub>k</sub>_

 is modeled as constant, since the phase ambiguities remain fixed so long as the receiver retains phase lock on each signal.

The filter ingests measurement vectors _y

<sub>k</sub>_

 for _k_ = 1, ..., _K_, each populated with a single epoch of double-differenced carrier phase measurements [![Eq-5](http://gpsworld.com/wp-content/uploads/2015/02/Eq-5.jpg)](http://gpsworld.com/wp-content/uploads/2015/02/Eq-5.jpg) for _i_ = 1, 2, . . . , _N

<sub>SV</sub>_

–1\. The filter's measurement model relates _y

<sub>k</sub>_

 to the real- and integer-valued state components through the following linearized GNSS carrier phase measurement model:

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Eq_3a.jpg%20360w,%20http://gpsworld.com/wp-content/uploads/2015/02/Eq_3a-250x106.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Eq_3a-300x128.jpg%20300w" class="alignnone size-full wp-image-34200" width="360">](http://gpsworld.com/wp-content/uploads/2015/02/Eq_3a.jpg)

 (3)

<span class="s6">where <em>r<sub>xk</sub></em> is a vector of double-differenced modeled ranges based on the filter’s real-valued state prior <a href="http://gpsworld.com/wp-content/uploads/2015/02/Eq-6.jpg">
  <img src="http://gpsworld.com/wp-content/uploads/2015/02/Eq-6.jpg" alt="Eq-6" class="alignnone wp-image-34203" width="22">
</a><em>,</em></span>

 _* 

<span class="s6">H</span>_

<sub>xk</sub> 

_and H_

<sub>n</sub> 

_are the measurement sensitivity matrices for the real- and integer-valued state components, and_ v

<sub>k</sub> 

_is the double-differenced measurement noise vector, all at time_ k*.

<span class="s4">
  <strong>Phase Residuals.</strong>
</span>

After processing data through the CDGNSS filter, the filter outputs, in addition to a time history of centimeter-accurate position estimates, a time history of phase residuals [![Eq-7](http://gpsworld.com/wp-content/uploads/2015/02/Eq-7.jpg)](http://gpsworld.com/wp-content/uploads/2015/02/Eq-7.jpg), which can be thought of as departures of each double-differenced phase measurement from phase alignment at the phase center of the antenna. These residuals can be modeled as

[![Eq_4a](http://gpsworld.com/wp-content/uploads/2015/02/Eq_4a.jpg)](http://gpsworld.com/wp-content/uploads/2015/02/Eq_4a.jpg) (4)<br>
where _r

<sub>xk</sub>_

 is now based on the filter's real-valued state estimate [![Eq-8](http://gpsworld.com/wp-content/uploads/2015/02/Eq-8.jpg)](http://gpsworld.com/wp-content/uploads/2015/02/Eq-8.jpg) at time _k_ and [![Eq-9](http://gpsworld.com/wp-content/uploads/2015/02/Eq-9.jpg)](http://gpsworld.com/wp-content/uploads/2015/02/Eq-9.jpg) represents the filter's estimate of the integer ambiguities at time _K_.

Phase residuals have been produced for batches of data collected from four different grades of antennas, as described next. These residuals will be used to analyze the suitability of each antenna for CDGNSS positioning.

**Antenna Performance Analysis**

This section describes four antennas from which data were captured and processed using the test architecture and CDGNSS filter described previously. It also quantifies the characteristics that make low-quality smartphone-grade antennas poorly suited to CDGNSS.

<span class="s2">
  <strong>Table 1</strong>
</span>

 describes a range of antenna grades of decreasing quality, noting properties relevant to CDGNSS. The loss numbers in the far-right column represent the average loss in gain relative to a survey-grade antenna, where the average is taken over elevation angles above 15 degrees.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Table1.jpg%20500w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Table1-250x123.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Table1-300x147.jpg%20300w" class="size-full wp-image-41426" width="500">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Table1.jpg)

 Table 1\. Antenna properties.

Survey-grade antennas, whose properties are described in the first row of Table 1, have a uniform quasi-hemispherical gain pattern, right-hand circular polarization, a stable phase center, and a low axial ratio. These are all desirable properties for CDGNSS. Unfortunately, these properties inhere in the antennas' large size; the laws of physics dictate that smaller antennas will typically be worse in each property.

The last row of Table 1 lists the properties for a smartphone-grade antenna. As shown subsequently, this antenna loses between 5 and 15 dB in sensitivity as compared to the survey-grade antenna. Such a loss makes it difficult to retain lock on GNSS signals. In addition, this antenna's linear polarization leads to extremely poor multipath suppression.

<span class="s4">
  <strong>Antenna Gain Analysis.</strong>
</span>

 

<span class="s2">
  <strong>Figure 2</strong>
</span>

 quantifies one of the obvious drawbacks of a smartphone-grade antenna, namely, its low gain.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-figure2.jpg%20514w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-figure2-250x196.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-figure2-300x235.jpg%20300w" class="size-full wp-image-34212" width="514">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-figure2.jpg)

 Figure 2, Drop in carrier-to noise ratio, from 2 hours of data and 9 tracked satellites. Antennas remained stationary.

The rightmost histogram, in green, shows that the decrease in carrier to noise ratio as compared to a survey-grade antenna is on average 11 dB, such that the smartphone-grade antenna only captures approximately 8 percent of the signal power as compared its survey-grade counterpart. For comparison, shown on the left, in blue, is a histogram of the decrease in carrier-to-noise ratio for the low-quality patch antenna. This antenna only suffers about a 0.6-dB drop in power on average relative to the survey-grade antenna. Each histogram was generated from 2 hours of data with nine tracked satellites ranging in elevation from 15 to 90 degrees. The antennas remained stationary. The variation in signal power around the means is due to the multipath-induced power variations in the signal as well as to the different gain patterns between each antenna and the survey-grade antenna.

<span class="s4">
  <strong>Phase Residual Analysis.</strong>
</span>

 Shown in 

<span class="s2">
  <strong>Figures 3, 4,</strong>
</span>

and

<span class="s2">
  <strong>5</strong>
</span>

 are 2,000-second segments of double-differenced phase residual time histories for data collected from a survey-grade, a low-quality patch, and a smartphone-grade antenna, respectively.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-3.jpg%20528w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-3-250x202.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-3-300x242.jpg%20300w" class="size-full wp-image-34210" width="528">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-3.jpg)

 Figure 3\. Survey-grade antenna. Each trace represents a residual for a different satellite pair. Ensemble average standard deviation 3.4 millimeters.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-4.jpg%20528w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-4-250x202.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-4-300x242.jpg%20300w" class="size-full wp-image-34209" width="528">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-4.jpg)

 Figure 4\. Low-quality patch antenna. Ensemble average deviation 5.5 mm.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-5.jpg%20528w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-5-250x202.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-5-300x242.jpg%20300w" class="size-full wp-image-34208" width="528">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-5.jpg)

 Figure 5\. Smartphone-grade antenna.Ensemble average deviation 11.4 mm.

To produce these residuals, the antenna position was locked to its estimated value within the CDGNSS filter. The residuals represent departures of the carrier phase measurements from perfect alignment at the average phase center of the antenna. Each different colored trace corresponds to a different satellite pair. While the data segments were not captured at the same time of day, they were captured at the same location, and thus the multipath environment was similar.

The ensemble average residual standard deviations increase with decreasing antenna quality. The residuals for the survey-grade, low-quality patch, and smartphone-grade antennas have ensemble average standard deviations of 3.4, 5.5 and 11.4 millimeters, respectively. This increase is due to the lower gain and less effective multipath suppression of the lower quality antennas.

Figure 5 shows the presence of outlier residuals in the data collected from the smartphone-grade antenna. These outliers, one of which persists for over 1,000 seconds, are likely caused by either large and irregular azimuth- and elevation-dependent antenna phase center variations or a combination of poor antenna gain in the direction of the non-reference satellite coupled with ample gain in the direction of a multipath signal such that the multipath signal is received with more power than the direct-path signal. Obvious outliers such as these can be automatically excluded by the CDGNSS filter via an innovations test. However, the standard deviation of the remaining residuals still remains large compared to that of the other antennas; the ensemble average standard deviation decreases from 11.4 to 8.6 millimeters upon exclusion of the two large outliers.

For antennas with a large ensemble average standard deviation in their double-differenced phase errors, the time correlation in the phase errors becomes more important. This time correlation, which persists for 100–200 seconds, is a well-studied phenomenon caused by slowly varying carrier phase multipath. While correlation is present in the residuals of all antenna types, and manifests approximately the same decorrelation time, its effect is more of a problem for low-quality antennas because the phase errors are larger. Such correlation, coupled with a large deviation, ultimately leads to a longer time to ambiguity resolution, shown later.

Given a smartphone antenna's extremely poor gain and multipath suppression as compared to even a low-quality stand-alone patch antenna, one might question the wisdom of attempting a CDGNSS solution using such an antenna. However, the next section reveals that it is indeed possible to achieve a centimeter-accurate positioning solution using a smartphone GNSS antenna despite its poor properties.

**CDGNSS with Smartphone Antenna**

<span class="s8">
  <strong>Figure 6</strong>
</span>

<span class="s9"> shows the result of an attempt to compute a CDGNSS solution using data collected from the GNSS antenna of a smartphone. The cluster of red near the top of the phone represents 400 CDGNSS position estimates over a 5-minute interval, superimposed on the photo and properly scaled. This cluster is referenced to a marker immediately under the phone whose position was surveyed to approximately 1-centimeter accuracy using a high-quality patch antenna. The mean of the cluster’s horizontal coordinates is approximately 2 centimeters from the phone’s internal GNSS antenna. Figure 6 shows the absolute horizontal accuracy of a CDGNSS solution through the smartphone’s antenna is approximately 2 centimeters.</span>

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-6.jpg%20419w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-6-250x221.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-6-300x265.jpg%20300w" class="size-full wp-image-34207" width="419">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-6.jpg)

 Figure 6 . Successful CDGNSS solution using data collected from smartphone antenna. The red cluster represents 400 CDGNSS solutions over 5 minutes, superimposed and properly scaled.

The data in Figure 6were collected with a large conductive backplane below the smartphone. However, the backplane is unnecessary. The

<span class="s2">
  <strong>opening photo</strong>
</span>

 shows the result of a CDGNSS positioning solution computed using data collected from the smartphone antenna while the device was held in the extended hand of the author. The cluster of red represents the computed 3-dimensional position of the phone over a 300-second interval, superimposed on the photo and properly scaled. The author's hand moved slightly during the interval, as reflected in the figure.

The opening photo also shows the residuals corresponding to the handheld CDGNSS solution. This shows how the residuals look in practice for a scenario in which the phone is held by a user. The residuals look fairly clean, that is, they have a small variance and their mean is approximately zero. It is not uncommon for the residuals to look this good; however, cases do arise in which the residuals are considerably worse due to a combination of poor antenna gain in the direction of the non-reference satellite, coupled with ample gain in the direction of a multipath signal.

The possibility of CDGNSS-enabled centimeter positioning using a smartphone antenna has been previously conjectured, but -- to our knowledge -- Figure 6 and the opening photo represent the first published demonstrations that this is indeed possible. This significant result portends a vast expansion of centimeter-accurate positioning into the mass market. However, serious challenges must be overcome before mass-market CDGNSS can become practical. Some of these challenges will be studied in the next few sections.

<span class="s4">
  <strong>Static Scenario.</strong>
</span>

<span class="s2">
  <strong>Figure 7</strong>
</span>

 shows the empirical probability of successful ambiguity resolution for data collected from four antennas, one of each of the different grades discussed earlier. For each antenna, seven satellites were tracked at approximately the same location and time of day. Each trace was computed from 12 batches of double-differenced carrier phase data.

Each trace represents an empirically-derived success rate computed from 12 batches of phase data as follows:

- For a given batch, at each epoch the filter outputs its best estimate of the integer ambiguities on the basis of the data ingested thus far.
- The estimate from step 1 is compared against the true set of integer ambiguities which were acquired in advance by processing a much longer batch of data. If correct, a flag is set at that epoch to "1"; if incorrect, the flag is set to 0.
- For each epoch, the flags produced in step 2 are averaged across all 12 batches to generate each trace.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-9.jpg%20522w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-9-250x206.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-9-300x248.jpg%20300w" class="size-full wp-image-34215" width="522">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-9.jpg)

 Figure 7\. Residuals for CDGNSS solution depicted in the opening photo.

As shown by the green trace in Figure 7, the smartphone-grade antenna required 400 seconds to achieve a 90% ambiguity resolution success rate; in other words, it manifested a 400-second TAR at 90%. This would surely exceed the patience of most smartphone users. Also shown are traces for the other three antenna grades. The higher-quality antennas yield shorter TARs for a given success rate, primarily due to their superior multipath suppression.

Note that the loss in received signal power due to the smartphone antenna's poor gain turns out to be tolerable -- the signals arriving from the smartphone-grade antenna can be tracked without cycle slipping. Therefore, the outstanding challenge preventing fast ambiguity resolution for data collected from smartphone-grade antennas is the severe time-correlated multipath errors in the double-differenced carrier phase data.

<span class="s4">
  <strong>Decreasing TAR via More Signals</strong>
</span>

. There are ways to mitigate the impact of multipath on the CDGNSS TAR, even the severe multipath experienced by low-quality antennas. It has been shown that the volume of the integer ambiguity search space, and thus TAR, decreases as a function of the number of double-differenced phase time histories available, which, for single-frequency CDGNSS, is one less than the number of satellites tracked. Consequently, an acceptable TAR can always be achieved with enough satellites tracked.

<span class="s2">
  <strong>Figure 8</strong>
</span>

 shows the reduction in TAR for an increasing number of satellites. Each trace was computed from 720 non-overlapping 2-minute batches of data taken from a survey-grade antenna over a 24-hour interval. A decreasing elevation mask angle was used to allow an increasing number of SVs to participate in the CDGNSS solution. For a given 2-minute batch of data, an elevation mask was first applied to all but the highest five satellites. Double-difference phase data from these satellites were then processed by the CDGNSS filter to compute an empirical probability of successful integer ambiguity resolution. Next, the elevation mask was reduced until one additional satellite was in view, and the process repeated to produce all traces shown.

Figure 8 makes clear that each additional double-differenced phase time history, although corrupted by its own multipath-induced phase errors, significantly decreases the overall TAR. Note that although Figure 8 was produced from data collected via a survey-grade antenna, a similar trend would apply for the smartphone-grade antenna. One implication of Figure 8 is that smartphone-based CDGNSS would benefit greatly from the additional double-differenced measurements that a multi-frequency GNSS receiver could provide. For example, at the time of writing there are 14 operational GPS satellites broadcasting unencrypted civil signals at the GPS L2 frequency (1227.6 MHz), and 7 broadcasting civil signals at the GPS L5 frequency (1176.45 MHz). With some modification of the smartphone GNSS antenna and chipset, these modernized GPS signals could be exploited to reduce TAR. However, the narrow profit margins on mass-market GNSS antennas and chipsets militate against multi-frequency architectures.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-10.jpg%20519w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-10-250x205.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-10-300x246.jpg%20300w" class="size-full wp-image-34216" width="519">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-10.jpg)

 Figure 8\. Probability of successful ambiguity resolution vs. time as a function of the number of satellite vehicles (SVs) tracked.

<span class="s4">
  <strong>Decreasing TAR via Random Motion.</strong>
</span>

There is a second way to reduce TAR under severe multipath conditions. Unlike TAR reduction via additional signals, the theory and practice of this second technique have not been previously treated in the literature. Moreover, the technique is well-suited for smartphones, which are typically hand-held and mobile. This simple technique consists of gently moving the smartphone in a quasi-random manner within a wavelength-scale volume. The key to this technique's effectiveness is that, whereas multipath-induced phase measurement errors are typically time-correlated on the order of hundreds of seconds for a static receiving antenna, their spatial correlation is on the order of one wavelength, or approximately 19 centimeters at the GPS L1 frequency. As a result, random wavelength-scale antenna motion transforms the phase residuals from slowly-varying when the antenna is static, as shown in 

<span class="s2">
  <strong>Figure 9</strong>
</span>

, to quickly-varying when the antenna is dynamic, as shown in 

<span class="s2">
  <strong>Figure 10</strong>
</span>

.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-91.jpg%20525w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-91-250x203.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-91-300x243.jpg%20300w" class="size-full wp-image-34221" width="525">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-91.jpg)

 Figure 9\. Residuals for data captured from smartphone-grade antenna while static.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-101.jpg%20525w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-101-250x203.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-101-300x243.jpg%20300w" class="size-full wp-image-34220" width="525">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-101.jpg)

 Figure 10\. Data from smartphone-grade antenna as it experienced wavelength-scale random motion, 2–5 cm/second.

Put another way, autocorrelation time of the phase residuals decreases from hundreds of seconds when the antenna is static, as shown in 

<span class="s2">
  <strong>Figure 11</strong>
</span>

, to less than a second when the antenna is moved even slowly (a few centimeters per second), as shown in 

<span class="s2">
  <strong>Figure 12</strong>
</span>

. More vigorous antenna motion would be possible if the phone's inertial devices were used to aid the phase tracking loops.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-11.jpg%20520w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-11-250x205.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-11-300x246.jpg%20300w" class="size-full wp-image-34219" width="520">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-11.jpg)

 Figure 11\. Autocorrelation functions corresponding to the phase residuals in Figure 9.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-12.jpg%20520w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-12-250x205.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-12-300x246.jpg%20300w" class="size-full wp-image-34218" width="520">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-12.jpg)

 Figure 12\. Autocorrelation functions corresponding to phase residuals in<br>
Figure 10.

<span class="s1">The shorter phase error decorrelation time resulting from random antenna motion effectively increases the information content per unit time that each double-differenced phase measurement provides to the CDGNSS filter, thus decreasing the time to ambiguity resolution. </span>

<span class="s2">
  <strong>Figure 13</strong>
</span>

 compares empirical success rates for three different antennas under static and dynamic scenarios. As expected, motion reduces the time-to-ambiguity resolution for the smartphone-grade and low-quality patch antenna. But, somewhat counterintuitively, motion increases the TAR for the survey-grade antenna. This discrepancy reflects a tradeoff within the CDGNSS filter. While it is true that the phase measurement errors decorrelate much faster when the antenna is moving -- increasing the per-epoch information provided to the filter -- it is also the case that the filter can no longer employ a hard motion constraint. For the high-quality antennas, the increased information per epoch due to faster phase error decorrelation is completely counteracted by a loss in information per epoch due to uncertainty (lack of constraint) in the motion model. Also, for the high-quality antennas, multipath in the reference antenna's phase measurements is not insignificant compared to multipath in the mobile antenna, and this reference multipath exhibits the usual 100–200 second correlation time for a static antenna. On the other hand, phase error decorrelation via random antenna motion offers the lower-quality antennas a larger net information gain because their multipath-induced phase errors are so large. Consequently, for the smartphone-grade antenna, motion substantially reduces the 90 percent success TAR, which drops from 400 to 215 seconds.

[<embed src="http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-13.jpg%20522w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-13-250x206.jpg%20250w,%20http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-13-300x248.jpg%20300w" class="size-full wp-image-34217" width="522">](http://gpsworld.com/wp-content/uploads/2015/02/Pesyna-Figure-13.jpg)

 Figure 13\. Probability of successful ambiguity resolution versus time for three different antennas under static and dynamic scenarios.

**Conclusions and Future Work**

<span class="s1">Centimeter-accurate positioning was demonstrated based on data sampled from a smartphone-quality GNSS antenna. An empirical analysis revealed that the extremely poor multipath suppression of these antennas is the primary impediment to fast resolution of the integer ambiguities that arise in the carrier phase differential processing used to obtain centimeter accuracy. It was shown that, for low-quality smartphone-grade GNSS antennas, wavelength-scale random antenna motion substantially reduces the ambiguity resolution time. </span>

<span class="s1">Future work will study the effectiveness of combining antenna motion with a motion trajectory estimate derived from non-GNSS smartphone sensors to further reduce the integer ambiguity resolution time. This technique, which is a type of synthetic aperture processing applied to the double-differenced GNSS phase measurements, effectively points antenna gain enhancements in the direction of the overhead GNSS satellites, thereby suppressing multipath arriving from other directions. Preliminary results show that this technique offers modest benefit beyond the unaided random motion technique discussed herein. </span>

**Acknowledgment**

The material in this article was first presented at ION GNSS+ 2014 in the paper "Centimeter Positioning with a Smartphone-Quality GNSS Antenna."

_<span class="s11">
  <strong>Kenneth M. Pesyna, Jr.</strong>
</span>

is a Ph.D. candidate in the Department of Electrical and Computer Engineering at the University of Texas at Austin. He is a member of the University of Texas Radionavigation Laboratory and the Wireless Networking and Communications Group._

_<span class="s11">
  <strong>Robert W. Heath, Jr.</strong>
</span>

 is a Cullen Trust Endowed Professor in Electrical and Computer Engineering at UT-Austin, and director of the Wireless Networking and Communications Group. He received his Ph.D. in electrical engineeringfrom Stanford._

_<span class="s11">
  <strong>Todd E. Humphreys</strong>
</span>

is an assistant professor in the department of Aerospace Engineeringand Engineering Mechanics at UT-Austin, and director of the UT Radionavigation Laboratory. He received a Ph.D. in aerospace engineering from Cornell University._
