# Biomimetic nestboxes for Sonoran Desert cavity-nesting birds

Data and analysis code for a study of artificial nest cavities designed to emulate the
thermal buffering of saguaro (*Carnegiea gigantea*) cavities in Sonoran Desert upland
habitat, southern Arizona, USA.

Three biomimetic prototypes were compared against a conventional wood nestbox, a hollowed
bottle gourd (*Lagenaria siceraria*), and two live saguaro cavities. This repository covers
the **thermal characterisation** half of the study: iButton temperature logging, the derived
buffering metrics, the cavity-type comparisons, and the construction plans for each prototype.

## Repository contents

```
analysis_r                            full analysis script (R)
data/
  raw_data.csv                        iButton logger records, all cavities
  temp_data_mason_center.csv          daily ambient maxima and minima
  temp_data_apr22_apr24.csv           15-min ambient series, 22-24 April (thermal-lag figure)
  tempdata_alldata_15min.csv          15-min ambient series, full study period
1_GC_Design1.pdf                      construction plan, Design 1
2_LH_Design2.pdf                      construction plan, Design 2
3_GK_Design3.pdf                      construction plan, Design 3
```

## Data description

### `data/raw_data.csv`

Temperature records from iButton loggers placed inside each cavity. 17,675 rows.

| column | description |
|---|---|
| `id` | cavity identifier (see mapping below) |
| `date_time` | timestamp, `M/D/YY H:MM` |
| `temp` | internal cavity temperature, **degrees Fahrenheit** |

Logging ran 18 April - 11 May 2023 at a nominal 15-minute interval. Observed temperatures
span 41.9-114.8 degF (5.5-46.0 degC).

Cavity identifiers map to cavity types as follows. Replicated prototypes carry a `-1` / `-2`
suffix; the analysis script averages replicates before analysis, so each prototype enters the
comparison as a single daily series.

| `id` | cavity type | loggers | coverage |
|---|---|---|---|
| `SAG1`, `SAG2` | live saguaro cavities (retained as independent units) | 1 each | 18 Apr - 11 May |
| `GC-1`, `GC-2` | Design 1 | 2 | 19 Apr - 11 May |
| `LH-1`, `LH-2` | Design 2 | 2 | 19 Apr - 11 May |
| `GK-1`, `GK-2` | Design 3 | 2 | 19 Apr - 11 May |
| `TB`, `TB-1`, `TB-2` | conventional wood nestbox | 1 then 2 | 18 Apr - 1 May, then 6-11 May |
| `G` | bottle gourd | 1 | 19 Apr - 30 Apr |

Note the uneven replication: the wood box is represented by a single logger (`TB`) for the
first part of the study and by two loggers (`TB-1`, `TB-2`) from 6 May, and the bottle gourd
by a single logger that stops on 30 April. The script collapses `TB`, `TB-1` and `TB-2` into
one averaged series.

### `data/temp_data_mason_center.csv`

Daily ambient maxima and minima (degrees Fahrenheit) for 19 April - 11 May 2023, 18 days,
from the reference station at the thermal-testing site (32.366, -111.048). Columns: `date`
(`M/D/YY`), `max_ambient`, `min_ambient`.

### `data/temp_data_apr22_apr24.csv` and `data/tempdata_alldata_15min.csv`

Ambient air temperature at 15-minute resolution in degrees Fahrenheit, with `date`, `time`
(12-hour clock) and `temp` columns. The first covers the three consecutive days used for the
continuous thermal-lag figure; the second covers all 18 days of the study period.

### Construction plans

`1_GC_Design1.pdf`, `2_LH_Design2.pdf`, `3_GK_Design3.pdf` are the build plans for the three
biomimetic prototypes. The filename prefixes match the logger `id` codes above (`GC` =
Design 1, `LH` = Design 2, `GK` = Design 3).

## Analysis

`analysis_r` runs the full thermal analysis in nine steps:

1. Import and clean the iButton records; drop 18 April, 19 April and 1 May 2023.
2. Average prototype replicates to one daily series per cavity type; saguaros kept separate.
3. Clean the ambient series and compute daily ambient range.
4. Merge, convert Fahrenheit to Celsius, and derive the three buffering metrics.
5. One-way ANOVA with Tukey HSD on each metric.
6. Build the publication summary table with significance letters.
7. Master temperature time-series figure.
8. Chi-square goodness-of-fit test on the orientations of used cavities.
9. 72-hour continuous thermal-lag figure.

### Derived metrics

All computed per cavity per day, in degrees Celsius:

- `delta_ambient_high_C` = cavity maximum - ambient maximum (daytime heat exclusion)
- `delta_ambient_low_C` = cavity minimum - ambient minimum (nocturnal heat retention)
- `range_difference_C` = ambient daily range - cavity daily range (overall buffering)

### Outputs

| file | contents |
|---|---|
| `Final_Cavity_Temperatures_Metric.csv` | daily per-cavity metrics in Celsius |
| `Table1_Thermal_Summary_C.csv` | summary table with Tukey significance letters |
| `tempdata_C.csv` | ambient series converted to Celsius |

## Requirements

R, with:

```r
install.packages(c("tidyverse", "lubridate", "multcompView"))
```

## Running the analysis

The script begins with an absolute `setwd()` pointing at a local Dropbox folder. Replace that
line with the path to this repository's `data/` directory, or delete it and run from within
`data/`:

```r
setwd("path/to/nestbox/data")
source("../analysis_r")
```

Output files are written to the working directory.
