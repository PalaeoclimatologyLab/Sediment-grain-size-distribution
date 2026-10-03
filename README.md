# Grain-Size Analysis of Beach Sediments

A MATLAB practical for analysing dry-sieved beach sediments from Pea Island National Wildlife Refuge, USA.

The script converts sieve openings from millimetres to phi (φ), calculates sample mass-balance error, and produces a histogram, cumulative retained-mass curve, and graphical grain-size statistics.

## Requirements

- MATLAB with support for `histogram`, `table`, and graphics ruler properties such as `MinorTickValues`.
- No external data files or additional toolboxes are required.

## Getting started

1. Download `beach_sediment_grain_size_analysis.m`.
2. Open the script in MATLAB.
3. Set the Current Folder to a writable directory.
4. Run the script.

Results appear in the Command Window and Figure 1. The script also saves a 300 dpi image named `grain_size_analysis.png` in the Current Folder. Running it again overwrites that image.

## Input data

The script contains the following sieve openings and retained masses:

| Sieve opening (mm) | Phi (φ) | Retained mass (g) |
|---:|---:|---:|
| 8 | −3 | 0.000 |
| 4 | −2 | 2.709 |
| 2 | −1 | 4.771 |
| 1 | 0 | 23.192 |
| 0.5 | 1 | 34.084 |
| 0.25 | 2 | 28.129 |
| 0.125 | 3 | 6.235 |
| 0.0625 | 4 | 0.880 |
| **Total** | | **100.000** |

The initial sample mass is **101.02 g**, and the measured recovered mass is **99.97 g**.

The listed fractions total **100.000 g**, which exceeds the measured recovered mass by **0.030 g**. The script reports this discrepancy and preserves the supplied values.

## Calculations

### Millimetres to phi

Grain diameter is converted using:

```matlab
phi = -log2(diameterMM);
```

Larger phi values indicate finer sediment.

### Mass-balance error

The percentage discrepancy between initial and recovered sample masses is:

```matlab
massBalanceErrorPct = ...
    abs(initialMass - recoveredMass) / initialMass * 100;
```

For this sample, the mass loss is **1.05 g**, and the mass-balance error is approximately **1.0394%**.

This describes sample recovery, not the uncertainty of individual grain-size measurements.

### Mass percentages

For the grain-size distribution, retained masses are normalized to the sum of the analysed fractions:

```matlab
massPct = massG / sum(massG) * 100;
```

This makes the cumulative distribution reach 100%. The mass-balance calculation remains separate.

### Cumulative retained mass

Mass percentages are accumulated from the coarsest to the finest sieve:

```matlab
cumulativePct = cumsum(massPct);
```

Each cumulative value is plotted at the finer boundary of its size class. For example, sediment passing the 8 mm sieve and retained on the 4 mm sieve occupies the interval φ = −3 to −2. Its cumulative value is plotted at φ = −2.

The curve represents **cumulative retained mass from coarse to fine**, rather than percent finer or percent passing.

### Graphical percentiles

The script estimates:

- φ5
- φ16
- φ50
- φ84
- φ95

Linear interpolation between adjacent cumulative-curve points provides the numerical equivalent of a graphical reading: move horizontally from the selected percentage to the curve, then vertically to the phi axis.

Percentiles outside the available cumulative range are not extrapolated.

### Mean, median, and sorting

The script uses the Folk–Ward graphical equations:

```matlab
meanPhi = (phi16 + phi50 + phi84) / 3;

medianPhi = phi50;

sortingPhi = (phi84 - phi16) / 4 + ...
             (phi95 - phi5) / 6.6;
```

Mean and median are also expressed as equivalent grain diameters:

```matlab
meanMM = 2^(-meanPhi);
medianMM = 2^(-medianPhi);
```

Sorting measures distribution spread and is reported in phi units. It is not a grain diameter, so its millimetre entry is shown as a dash in the statistics table.

### Modal-class midpoint

The modal class is the interval with the greatest retained mass. Its midpoint in phi is used as an estimate of the modal value.

For the supplied data, the modal class is φ = 0 to 1, giving a midpoint of **0.50 φ**.

## Figure contents

Figure 1 includes:

- A transparent magenta histogram with the modal-class midpoint marked.
- A black cumulative curve with magenta sieve-data markers.
- Solid construction lines for the five graphical percentiles.
- White percentile markers with black edges.
- Blue percentile labels with white backgrounds.
- Dotted gridlines at regular major and minor ticks.
- An input-data table.
- A percentile and grain-size statistics table.

## Using another sample

Edit the input section:

```matlab
diameterMM = [...];  % Sieve openings in millimetres
massG = [...];       % Corresponding retained masses in grams

initialMass = ...;
recoveredMass = ...;
```

Each sieve opening must have one corresponding retained mass. The script sorts the paired values from coarse to fine.

The current histogram layout requires zero retained mass on the coarsest sieve, which provides the starting boundary. If that sieve contains sediment, define an appropriate coarser boundary before adapting the histogram.

The modal-class calculation assumes equal-width intervals in phi. Unequal class widths require reconsidering how the modal class is identified.

When changing the sample, check the figure layout for overlapping labels.

## Formatting and export

Font size, weight, colour, and position are specified beside each individual title, axis label, or annotation.

The final command exports the figure:

```matlab
print(fig, 'grain_size_analysis.png', '-dpng', '-r300');
```

Comment out this line to disable automatic export.

## Reference

Folk, R. L., and Ward, W. C. (1957). Brazos River bar: A study in the significance of grain size parameters. *Journal of Sedimentary Petrology*, **27**(1), 3–26.

## Author

**Gianluca Marino**  
Palaeoclimatology Lab, GEOMA  
Centro de Investigación Mariña, Universidade de Vigo, Spain

- gianluca.marino@uvigo.es
- gianluca.marino@uvigo.gal
