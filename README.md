# SC22 Tag Analysis

This repository contains reproducible Quarto analyses of the treatment of mixing-period tag recaptures in the 2026 WCPO bigeye assessment.

## Reports

- [mixing-period-tags-summary.qmd](https://n-ducharmebarth-noaa.github.io/sc22-tag-analysis/mixing-period-tags-summary.html) is an executive summary examining what
  `tag_flags(i,2)` can and cannot do, using `bet.tag` and fitted reporting
  rates.
- [mixing-period-tags-v10.qmd](https://n-ducharmebarth-noaa.github.io/sc22-tag-analysis/mixing-period-tags-v10.html) is a more in depth simulation investigation comparing
  `tag_flags(i,2)` settings with likelihood-based alternatives in MULTIFAN-CL.

Rendered HTML versions of both reports are included in the repository. The analyses use input snapshots in `inputs/`, including `bet.tag`, `final.par`, and a cached source revision for reproducible rendering.

To render a report locally, use Quarto with R installed:

```powershell
quarto render .\mixing-period-tags-summary.qmd
quarto render .\mixing-period-tags-v10.qmd
```

## 2026 WCPO bigeye tuna assessment evaluation (DRAFT)

### The Good

- Guidance provided during SC19 discussions of BET and at the PAW were taken on board and explored: simplification of the spatial structure, external analysis of M, internal estimation of growth with additional age-at-length data, more scrutiny of the size composition data, effort creep and an alternative approach to setting the regional weighting.
- Openness, reproducibility and transparency. Having model files (inputs and outputs) available for interrogation and review is much appreciated and greatly facilitates the review process.
- Enhanced diagnostics include simulation self-test, jittering, reports on convergence and parameter correlations as well as many standard model diagnostics improve our ability to evaluate the model.
- Sensitivity analyses covered off a number of important areas related to the treatment of the tagging data (tau, tag mixing cutoff, inclusion of reporting rates during the mixing period), and regional scaling.
- Additionally, information papers produced by SPC investigated spatially implicit, fleets-as-areas models as well as the potential effect of spatially varying growth, both of which were identified in previous discussions as either critical areas of development or future research.

**Net result:** Quite possibly the best assessment SPC has ever produced. The transparency and openness of the assessment development process has also allowed for a more thorough investigation of the stock assessment model than ever before, allowing the SC to truly fulfill its role as a scientific review body. Unfortunately, this resulted in the discovery of a fundamental flaw in how Multifan-CL deals with the tagging data, and a number of other issues with the stock assessment.

### Areas for improvement

- As noted by the authors jittering, simulation self-test, parameter correlations & parameter bounds checks identified that despite simplifications, several parameters related to the model's spatially explicit structure remained poorly determined and weakly identifiable.
- Indicated retrospective bias in biomass and depletion indicates that a model process may be inappropriately assumed as stationary.
- The model appears to offset increasing regional catches (regions 2–5) with increased recruitment. This could either be evidence of model mis-specification with the model using the regional recruitment as an escape valve to deal with extra process variation that other components are not sufficiently flexible to deal with OR this is a genuine phenomenon and juvenile survival has improved (e.g., release of predation mortality). In the case of the former, having the recruitment time series absorb the unmodeled process will contaminate estimates of population scale and depletion.
- Movement rates are age/length invariant. BET likely have different movement dynamics as a function of age/length and not accounting for this has implications for biomass flows within a spatially explicit assessment.
- Data conflict remains an issue.

### Critical issues

#### Tier 1: Inclusion of tagging data in Multifan-CL

Multifan-CL does not appropriately model tags within the mixing period, and analysts are forced to choose between two flawed options. Including a reporting rate adjustment within the mixing period may produce unbiased results under certain conditions but is a fragile option. The reporting rate must be correct, tag release groups must be large enough to be informative, and the correction must not result in too few tags surviving the mixing period. The alternative within Multifan-CL is to not adjust for the reporting rate during the mixing period, and implicitly assume that reporting rate is 1. This is a strong assumption, and one that is unlikely to be true given an inspection of the actual tag return data. Additionally, it has a known directional bias and will never remove more tags than the raised option. Whenever the reporting rate is below one and the raised removal is feasible, this produces lower estimates of F and larger estimates of population scale. In either case, both models deterministically allow the tags recaptured within the mixing period to impact the tagged population dynamics without being formally incorporated into the likelihood or contributing to parameter uncertainty; both of which have implications for the estimated population dynamics. This is inappropriate and in practice means a meaningful proportion of the tagging data silently impacts model results.

If tagging data is to be used in Multifan-CL, the software needs to be revised to add a dedicated mixing period tagging likelihood with the additional estimation of mixing-period F and availability. However, this revision would add additional complexity, and it remains to be seen if the additional parameters would be identifiable especially under the current reality of many small, noisy release groups.

#### Tier 2: Retaining the reporting rate correction (included v. excluded) axis within the uncertainty grid

The current proposed uncertainty grid includes both mixing period tag reporting rate adjustments. As mentioned above, these options are both incorrect so it would be preferable to not inform management from models fitting to tagging data. However, if we are in a position where we are forced to provide management advice from a model that fits to tagging data we should only present models that implement one of the options, not both. These options are structurally one-sided. Assuming complete reporting of tags during the mixing period (not adjusting for reporting rate) can not mathematically remove more tags from the population than adjusting for reporting rate. This has implications for estimated F and population scale. If estimated F increases as a function of the ratio of recaptured to total tags (likely) this means that making that assumption introduces a cap to the estimated F relative to adjusting for reporting rate. In practice this means that including both options is unlikely to actually bound estimates of F and/or population scale.

If we are forced to choose an approach, we should choose models that make the reporting rate adjustment within the mixing period. The rationale for this is that, while this option is fragile, it is technically capable of producing an unbiased F estimate assuming the aforementioned conditions are met. Assuming complete reporting of tags will always produce an F estimate that is biased low unless the reporting rate within the mixing period is 1. Inspection of the current assessment tag file and estimated reporting rates indicates that reporting rate is unlikely to be 1 for a large component of the tagging data.

### Path forward

Given the issues with the tagging data, there are two clear, and independent, paths forward.

#### 1. Modify Multifan-CL

Multifan-CL must be modified to include an additional likelihood to appropriately deal with the tag recaptures during the mixing period, and allow for the estimation of mixing period F and availability (by tag group).

**Pros**

- Correctly integrates the tagging data into the model allowing for errorpropagation and mixing period assumptions to compete in the likelihood along with other model components.

**Cons**

- Adds complexity.
- Additional parameters may be weakly identifiable given tag release groups tend to be small and noisy.

#### 2. Include spatially implicit, fleets-as-areas models in the uncertainty grid

Management advice should be formulated from a structural uncertainty grid that includes spatially implicit, fleets-as-areas models (with the extension to exclude size-based composition data to age-composition data using spatially explicit age-length keys).

**Rationale**

A spatially implicit fleets-as-areas model was developed by SPC (SC22-SA-IP-09) and presented side-by-side with the proposed 2026 diagnostic case model as Appendix A of the report (SC22-SA-WP06-REV1). This comparison showed that with respect to key management quantities the fleets-as-areas model was able to produce estimates comparable not only to the spatially-explicit diagnostic case model (which downweighted the influence of the tagging data), but also to two additional spatially explicit models fit without tagging data and assuming fixed movement rates (MFCL & SEAPODYM).

The main criticisms of the fleets-as-areas model in SC22-SA-IP-09 were that it was a more pessimistic model than the spatially-explicit baseline (the presumed hypothesis being that the spatially implicit structure is unable to appropriately capture spatial heterogeneity in fishery and population dynamics), and that the model had a strong retrospective pattern. Neither criticism has held up. Relative to the diagnostic case, both spatially implicit and spatially explicit models estimate similar fishery and population dynamics indicating that a fleets-as-areas model is capable of replicating the result from a more complicated estimating model. Additionally, both models show similar strong retrospective patterning. This indicates that the retrospective pattern is not induced by the spatial model configuration but is an aspect of the data and stationary model process that both configurations fail to capture.

Furthermore, the spatially implicit fleets-as-areas approach can more feasibly allow analysts to address the problem of spatially varying growth following SPC's analysis of the issue (SC22-SA-IP-11). All composition data can be converted using spatial age-length keys and input to the model as age data. This would address some of the additional criticism of the spatially implicit modelling approach raised in SC22-SA-IP-09, noting that that criticism applies equally to both spatially implicit and explicit modeling approaches since they both assume a single growth curve.

**Pros**

- Including spatially implicit models in the provision of management advice side-steps the aforementioned issue with the tagging data. It also integrates over uncertainty in estimation model spatial structure, stock and movement hypotheses, as well as the sensitivity of management advice to the tagging data which we have established is poorly treated.
- It is a substantially simpler and less complex modeling approach. This should yield benefits in terms of computational costs and model development timelines.
- The overall reduction in model complexity will allow analysts to tractably and strategically increase model complexity (e.g., estimate time-varying selectivity) in order to resolve potential model mis-specification (e.g., retrospective bias, and pattern of increasing recruitment that coincides with increasing purse seine and ID/PH catch).

**Cons**

- Spatially implicit modeling approach is a simplification of population and fishery dynamics. Future simulation testing is needed to understand both a) the limitations of spatially implicit modelling approaches and b) the implications of fitting spatially explicit models when parameters are poorly determined by the data or key simplifying assumptions are made (e.g., age/length invariant movement).
- Information from tagging data is not directly incorporated into the model. However, it can still be used to inform prior distributions for mortality parameters (M, F or Z).

## Disclaimer

This repository is a scientific product and is not official communication of the National Oceanic and Atmospheric Administration, or the United States Department of Commerce. All NOAA GitHub project code is provided on an 'as is' basis and the user assumes responsibility for its use. Any claims against the Department of Commerce or Department of Commerce bureaus stemming from the use of this GitHub project will be governed by all applicable Federal law. Any reference to specific commercial products, processes, or services by service mark, trademark, manufacturer, or otherwise, does not constitute or imply their endorsement, recommendation or favoring by the Department of Commerce. The Department of Commerce seal and logo, or the seal and logo of a DOC bureau, shall not be used in any manner to imply endorsement of any commercial product or activity by DOC or the United States Government.