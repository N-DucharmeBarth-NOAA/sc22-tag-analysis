# SC22 Tag Analysis

This repository contains reproducible Quarto analyses of the treatment of
mixing-period tag recaptures in the 2026 WCPO bigeye assessment.

## Reports

- `mixing-period-tags-summary.qmd` is an executive summary examining what
  `tag_flags(i,2)` can and cannot do, using `bet.tag` and fitted reporting
  rates.
- `mixing-period-tags-v10.qmd` is a companion simulation study comparing
  `tag_flags(i,2)` settings with likelihood-based alternatives in MULTIFAN-CL.

Rendered HTML versions of both reports are included in the repository. The
analyses use input snapshots in `inputs/`, including `bet.tag`, `final.par`,
and a cached source revision for reproducible rendering.

To render a report locally, use Quarto with R installed:

```powershell
quarto render .\mixing-period-tags-summary.qmd
quarto render .\mixing-period-tags-v10.qmd
```

## Disclaimer

This repository is a scientific product and is not official communication of the National Oceanic and Atmospheric Administration, or the United States Department of Commerce. All NOAA GitHub project code is provided on an 'as is' basis and the user assumes responsibility for its use. Any claims against the Department of Commerce or Department of Commerce bureaus stemming from the use of this GitHub project will be governed by all applicable Federal law. Any reference to specific commercial products, processes, or services by service mark, trademark, manufacturer, or otherwise, does not constitute or imply their endorsement, recommendation or favoring by the Department of Commerce. The Department of Commerce seal and logo, or the seal and logo of a DOC bureau, shall not be used in any manner to imply endorsement of any commercial product or activity by DOC or the United States Government.