---
layout: post
title: Property Tax Impacts of Maple Valley Prop. 1 - Methodology
---

This article describes the methodology we used to make [our predictions](http://demportal.org/mv_prop_1) for the fiscal impacts of the new property tax proposed by [Maple Valley Prop. 1](http://www.kingcounty.gov/depts/elections/how-to-vote/ballots/whats-on-the-ballot/ballot-measures/february-special/list-of-measures/maple-valley.aspx).
We presented the impacts as "Annual Property Tax" and "Percent of Income Spent on Housing", because we thought these would be the most useful variables for voters to broadly understand the fiscal impacts of this measure on their community.
This is our first foray into policy predictions, and our numbers for this measure should be interpreted as a best effort on short notice, not as highly-reliable.
Any official city estimates which contradict our predictions should be preferred over ours.

The [election website](http://www.kingcounty.gov/depts/elections/how-to-vote/ballots/whats-on-the-ballot/ballot-measures/february-special/list-of-measures/maple-valley.aspx) included an estimate that the tax rate would be increased by $0.35 per $1000 of home value.
The difficulty we faced is that joint income and home values are not publicly available to do this calculation.
So, our method was basically two steps:
<ul>
<li>Jointly estimate incomes and home values</li>
<li>Compute fiscal impact given estimated home values and incomes</li>
</ul>

## Data Sources

The [King County election website](http://www.kingcounty.gov/depts/elections/how-to-vote/ballots/whats-on-the-ballot/ballot-measures/february-special/list-of-measures/maple-valley.aspx) included an estimate that property taxes would be increased by $0.35 per $1000 of home value.

We used data from the 2015 American Community Survey Data (Produced Annually by Census Bureau).
We used the 2015 ACS because the 2016 results aren't available yet at the time of our analysis - we made no attempt to extrapolate 2016 numbers.
We used data from the following two pages: [employment and income information for Maple Valley](https://factfinder.census.gov/bkmk/table/1.0/en/ACS/15_5YR/DP03/1600000US5343150), and [housing information for Maple Valley](https://factfinder.census.gov/bkmk/table/1.0/en/ACS/15_5YR/DP04/1600000US5343150).

We used the [2015 Maple Valley Final Budget](http://www.maplevalleywa.gov/home/showdocument?id=7826) property tax rate of $1.25 / $1000.

## Computing Fiscal Impact Given Estimated Home Values and Incomes

Input to this stage is a row of variables representing a single household:

Income | Housing costs | House type (rent, mortgage, no mortgage) | Home value (if not renting)

The [election website](http://www.kingcounty.gov/depts/elections/how-to-vote/ballots/whats-on-the-ballot/ballot-measures/february-special/list-of-measures/maple-valley.aspx) included an estimate that the tax rate would be increased by $0.35 per $1000 of home value.

For each household, we create two new rows to show alternative outcomes depending on whether Prop. 1 passes.
If the household is not a rental, we added to their housing costs $0.35 * (home value) / $1000 to their housing costs if vote outcome is "yes".
For renters, there is no difference to housing costs between a yes and no vote (their landlords may decide to raise rents in response to the tax hike, but we did not account for this).

## Jointly Estimating Incomes and Home Values

We randomly generated a simulated population of 8848 households whose statistical properties matched the information available to us through our various sources using a [Metropolis Algorithm](https://en.wikipedia.org/wiki/Metropolis%E2%80%93Hastings_algorithm) and the [Bayesian Network](https://en.wikipedia.org/wiki/Bayesian_network) representation of houshold characteristics shown in Figure 1. 
The software we wrote to do these predictions is freely available for your inspection, modification, and redistribution [here](https://github.com/better-dem/property-tax-effects-calculator).

<img src="https://raw.githubusercontent.com/better-dem/better-dem.github.io/master/images/mv_bn_household_representation.png">

Distributions <i>p<sub>C</sub>(i)</i>, <i>p<sub>C</sub>(t)</i>, <i>p<sub>C</sub>(v)</i>, <i>p<sub>C</sub>(c|t)</i>, and <i>p<sub>C</sub>(p|t)</i> were all available as histograms from the census sources above (<i>p<sub>C</sub></i> denotes the Census distribution). <i>p(x|t,v)</i> is a deterministic formula (multiply the tax rate by the home value if the home type is not "rent"), as is <i>p(p|i,c,t)</i> (compute <i>c / t * 100</i>, the percentage).

The Metropolis algorithm requires a generation distribution <i>g(y'|y)</i>, and an acceptance ratio <i>&alpha;(y',y) = A(y')/A(y) = p(y')/p(y)</i>. 
We use a gaussian random walk to generate candidates.
Our acceptance score <i>A(y)</i> for a sample <i>y</i> was calculated using the formula: <i>A(y) = Indicator(c<sub>y</sub> &lt; x<sub>y</sub>) * p<sub>C</sub>(p<sub>y</sub>|t<sub>y</sub>)</i> * <i>p<sub>C</sub>(i)</i> * <i>p<sub>C</sub>(t)</i> * <i>p<sub>C</sub>(v)</i> * <i>p<sub>C</sub>(c|t)</i> * <i>p<sub>C</sub>(p|t)</i>.

For our final estimates, we ruled out outliers beyond 3 standard deviations along the home value or income dimensions.


## Conclusion and Future Work
This was Better Democracy Network's first attempt at doing policy predictions and making the results easily-digestible to voters in advance of an election.
The prediction aspect of this work was very simple, and the majority of work went into jointly estimating information that is only available as marginal histograms.

There are a large number of weaknesses in this work which we want to begin to address in future attempts.
First, we hope to engage the cities in getting their input and access to numbers which maybe aren't published.
Additionally, we want to generalize the software written here to easily allow more variables to be introduced to constrain our estimates.
For example, the city of Maple Valley publishes its property tax revenues, which we should be able to use to improve our estimates.
