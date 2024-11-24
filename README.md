java c
Problem set 4 - EC 356
Note: You have to submit an electronic version of your homework as a PDF on black-board. This problem set has to be typed up using a Word Processor (MS Word, Google Docs, ...), then converted to a PDF. You can copy and paste Stata output into your word file either by taking screen shots. If you copy and paste stata output as text use a fixed width (or typewriter) font like “Courier New” so the table looks better. Make sure your write-up is legible and clearly structured.
Important: Late submissions will not be accepted. Make sure you use backups and leave enough time for mishaps (scanner not working, your computer breaks, ...).
Introduction
This problem set lets you replicate parts of the analysis in Schmieder, von Wachter and Bender (2012, QJE). Since the original data is not publicly available without special permission, the problem set is based on a synthetic dataset that has loosely the same characteristics as the data used in the paper and should allow you to come up with similar results.
The variable expbaseline contains the "Number of Months worked in previous 7 years". This determines potential UI durations P in Germany (have a look at the paper), essentially for 2 months of experience people get 1 month of UI. However there are age specific caps, as we talked about in class (and described in the paper). The results in the paper are based only on people who would be eligible to the maximum P if they had been 49 years or older, that is people who had at least 52 months of exp at baseline. The dataset you have contains people with less experience and we will later restrict to this subsample of high experience workers.
Take a look at the following paper:
• Schmieder, von Wachter and Bender (2012). "The effects of extended unemployment insurance over the business cycle: evidence from regression discontinuity estimates over 20 years." Quarterly Journal of Economics.
• You don’t have to follow everything in the paper, but we will create some of the figures and tables you can find in there.
Problem 1: Descriptive Analysis
Summarize the dataset.
a) Create a summary table that shows the mean and standard deviations for the variables that you have in your dataset.
b) Create a graph of Potential UI benefit durations P by age. You can use similar code as in Problem Set 3, when you created a graph of the federal minimum wage by year: 1) collapse the data by age, then 2) use the twoway line command to create the figure.
c) Create another graph of Potential UI benefit durations P by age, but this time use only observations such that expbaseline >= 52.
Problem 2: OLS Analysis
Note that for this next part you have to reload the dataset.
a) Estimate the effect of potential UI durations on nonemployment durations using OLS. For this purpose run a regression of non-emp duration on P, without controls. Then add more control variables that you can find in the dataset. Create a typical regression table where each column is one regression. You can use the eststo and esttab commands that you used for problem set 3.
b) What do you notice about the coefficient? What is the estimated effect of potential UI durations on nonemployment duration?
c) What is a possible omitted variable bias in these regressions? Explain carefully, why we might not want to trust these results.
Problem 3: Regression Discontinuity Design
Next we use regression discontinuity design to analyze the effect of P on nonemployment duration. For this we have to focus on the subsample for whom the age discontinuity is relevant, that is people who are at the maximum UI durations. For this drop all people for whom “expbaseline” is less than 52.
drop if expbaseline < 52
You should save the resulting dataset in a temporary file called “uidata_temp.dta”, since the below code will modify the data several times and you will have to reload the data.
a) Create a density plot of the data similar to Figure II a) in the paper. For this we need to create b代 写EC 356 Problem Set 4R
代做程序编程语言ins of 15 day age windows using the following code:
local binsize 15
g agebins = floor((agedays - (42*365.25))/‘binsize’)/(365.25/‘binsize’) +
42
Note that the first quote before ‘binsize’ is the single quote underneath the ~ sign on a regular keyboard, the second one is the single quote next to the Enter key. Alternatively you can just replace ‘binsize’ with the number 15 in that command.
For the sake of simplicity we ignore the problem of leap years here (it’s a real pain to correct for that).
Now collapse the data to the bin level using something like the following code:
g N = 1
collapse (count) N, by(agebins)
The resulting data has the number of observations for each age bin.
Now you can create a scatter plot that should look similar to Figure II a):
twoway scatter N agebins if inrange(age,40,49), xline(42 44 49)
Note that the xline(42 44 49) option adds vertical lines where the age discontinuities are.
b) Visually check whether there is an increase in nonemployment durations at the age cutoffs. You will have to reload the data “uidata_temp.dta” from above.
Create a figure similar to Figure III a) in the paper. Use a binsize of 120 days (as in the paper, note you have to create a different agebins variable). The code is very similar as for the density plot above. Note that the collapse command can compute different statistics - we want the mean now instead of the count, so you can use:
collapse (mean) durnonemp, by(agebins)
How big is the increase in non-employment durations at the age cutoffs? Note that at the age cutoffs P increases by 6 months at the age 42 cutoff and 4 months at the others.
What would be your (rough) estimate of the effect of P on non-employment durations based on the figure? How does that compare to the regression results from above?
c) Investigate whether predetermined characteristics are smooth around the threshold. Do this by replicating the Figures from b) but for various predetermined characteristics:
expbaseline edyrs female and tenure.
Based on these results and the density plots, what can you say about the identification assumptions needed for the RD design? Are they plausible?
From here on we use regression analysis. To make things a bit easier, focus only on the age 42 cutoff. Simply drop all observations where age<40 or age>=44.
d) Regression Analysis:
Estimate the effect of P on nonemployment durations using the RD design. Do so by controlling linearly for age in days (agedays) but allow for a different slope on both sides of the cutoff. For this first generate a dummy for being to the right of the cutoff and an interaction term for agedays and this dummy:
g RD = age>=42
g a0 = agedays-(42*365.25) // age control variable normalized so that cutoff
is at 0
g a1 = a0 * RD // age control interacted with the discontinuity
Now run the regression:
reg durnonemp RD a0 a1
This is the basic specification, from Table II Panel B in the paper, for the Age 42 cutoff.
In order to get the marginal effects (effect of 1 month in crease in P) you can either divide the estimated coeffecients on RD by 6 or run directly
reg durnonemp P a0 a1
Compare these results with the results in the OLS section above.
e) Estimate the same regression as in d) but using smaller windows around the cutoff. In d) we used a 2 year window on each side of the age 42 cutoff. Estimate the same specification for a 1, 0.5 and 0.2 year window.
To restrict to a 0.5 year window you can use code like:
reg durnonemp RD a0 a1 if (abs(a0) /365.25) < 0.5
For other windows replace the 0.5 with other numbers.
Create a table that shows your estimates similar to Table II Panel B in the paper (but only for the age 42 cutoff). You can again use the eststo and esttab commands for this.
f) In your own words, how do the results from the OLS and RD analysis compare? What are the advantages of RD vs. OLS? Can you think of disadvantages?





         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
