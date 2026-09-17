# Modelling Wind Turbine Power Output from Hourly Wind Speed Data

This repository contains a set of Jupyter notebooks for estimating electrical power output from a small horizontal-axis wind turbine and for extending the analysis to a wind farm. The work combines an empirical power-curve model, a Weibull characterisation of the wind resource, and simulations based on the `windpowerlib` library.

## Objectives

- Construct a deterministic power curve for a 25 kW turbine from hourly 10 m wind speed.
- Estimate Weibull shape (\(k\)) and scale (\(c\)) parameters of the observed wind-speed distribution.
- Reproduce single-turbine generation using `windpowerlib` and meteorological inputs.
- Scale the representation to a wind-farm configuration with wake-aware cluster modelling.

## Turbine parameters

| Parameter        | Value        |
|------------------|--------------|
| Rated power      | 25 kW        |
| Rated wind speed | 7.7 m/s      |
| Cut-in speed     | 2.5 m/s      |
| Cut-out speed    | 20 m/s       |
| Blade length     | 7.6 m        |
| Rotor area       | 181 m²       |
| Hub height       | 16 m         |

## Data

- `Wind_Speed.csv` — hourly 10 m wind speed (`WS10M`), calendar fields YEAR, MO, DY, HR (8760 records).
- `weather_montreal.csv` — multi-height weather time series (pressure, temperature, wind speed, roughness length) used by `windpowerlib`.

## Notebooks

1. **Single_Turbine_Model1.ipynb**  
   Reads `Wind_Speed.csv`, applies cut-in / rated / cut-out limits, computes hourly power, and plots the speed–power relationship.

2. **Wind_Distribution.ipynb**  
   Fits a two-parameter Weibull density to the observed speeds using the coefficient-of-variation estimator for \(k\) and the gamma function for \(c\).

3. **Single_Turbine_Model2.ipynb**  
   Instantiates a `WindTurbine` and `ModelChain` in `windpowerlib` and evaluates power from the Montreal weather file.

4. **Wind_Farm_Model.ipynb**  
   Builds a `WindFarm` / `TurbineClusterModelChain` representation and simulates aggregated farm output.

## Method notes

Power below cut-in and above cut-out is set to zero. Between cut-in and rated speed the first notebook uses a speed-dependent mapping consistent with the stated 0.855 kW at 2.5 m/s and 25 kW at 7.7 m/s. The Weibull fit follows

\[
k = \left(\frac{\sigma}{\mu}\right)^{-1.089},\qquad
c = \frac{\mu}{\Gamma(1+1/k)}.
\]

`windpowerlib` simulations use hub-height interpolation, roughness length, and the library power-curve / power-coefficient routines rather than the hand-built curve.

## Requirements

- Python 3  
- pandas, numpy, matplotlib, scipy  
- windpowerlib  

## Usage

Place the CSV files beside the notebooks and execute the cells in order. The first two notebooks depend only on `Wind_Speed.csv`; the `windpowerlib` notebooks require `weather_montreal.csv`.

## Limitations

Results are site- and turbine-specific. The custom curve does not include air-density correction, turbulence intensity, or availability. Farm results inherit the wake and efficiency assumptions of `windpowerlib` and should not be treated as a layout-optimised yield study.
