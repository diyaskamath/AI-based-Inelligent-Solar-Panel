# AI-Based Intelligent Solar Panel Monitoring, Self-Adaptive Cleaning & Predictive Fault Diagnosis

An AI-powered solar panel monitoring system that combines Machine Learning, a Digital Twin, IoT simulation, and MQTT communication to identify potential panel faults and recommend condition-specific actions.

## Overview

Solar panels can lose efficiency due to dust accumulation, partial shading, and localized thermal hotspots. However, a reduction in power output alone cannot reliably identify the underlying cause.

This project addresses that challenge by integrating electrical feature extraction, Machine Learning classification, Digital Twin-based power analysis, and independent thermal anomaly detection into a unified monitoring workflow.

The system classifies the panel condition, compares expected and actual power, checks whether power loss persists, and recommends an appropriate action. The current implementation is primarily software-based, with the IoT hardware and actuator workflow simulated.

## Objectives

- Monitor solar panel electrical performance.
- Extract important features from photovoltaic I–V data.
- Classify panel conditions using Machine Learning.
- Estimate expected power using a Digital Twin.
- Detect persistent power deviations and localized thermal anomalies.
- Recommend cleaning or tilt adjustment when appropriate.
- Publish system status, alerts, and actions through MQTT.

## Key Features

### 1. Solar Panel Data Analysis
Processes photovoltaic data and extracts electrical characteristics such as:

- Open-circuit voltage (\(V_{oc}\))
- Short-circuit current (\(I_{sc}\))
- Maximum power point voltage (\(V_{mpp}\))
- Maximum power point current (\(I_{mpp}\))
- Maximum power (\(P_{mpp}\))
- Fill factor

The system also generates I–V and P–V curves for performance analysis.

### 2. Machine Learning-Based Fault Classification
A Random Forest classifier identifies three panel conditions:

- **Healthy/Clean**
- **Soiling/Dusty**
- **Partial Shading**

The model uses extracted electrical features to distinguish between operating conditions.

### 3. Digital Twin and Power Deviation Analysis
The Digital Twin estimates expected panel power using irradiance and temperature inputs. It compares this estimate with actual electrical power.

Actual power:

\[
P_{act}=V\times I
\]

Power deviation:

\[
\Delta P\%=
\frac{P_{exp}-P_{act}}{P_{exp}}\times100
\]

This comparison helps identify performance loss that may require further diagnosis.

### 4. Thermal Hotspot Screening
A virtual AMG8833-style thermal grid represents 64 temperature readings arranged in an 8×8 grid.

The hotspot screening rule is:

\[
\Delta T=T_{max}-T_{mean}\geq8^\circ C
\]

A detected thermal anomaly triggers a separate safety alert. Hotspot detection is independent of the three-class Machine Learning classifier.

### 5. Condition-Based Decision Engine
The system uses the predicted condition, power deviation, persistence, and hotspot flag to recommend actions:

| Detected condition | Recommended action |
|---|---|
| Healthy/Clean | Continue monitoring |
| Persistent soiling | Recommend simulated cleaning |
| Partial shading | Request panel tilt adjustment |
| Thermal hotspot | Raise an alert and inhibit cleaning |

Cleaning is recommended only when the soiling condition is identified and power deviation is at least 10% for three consecutive cycles.

### 6. MQTT Communication
MQTT provides lightweight publish/subscribe messaging for sharing structured JSON information, including panel status, power deviation, alerts, and recommended actions.

### 7. Simulated Recovery Verification
After simulated cleaning, the system compares the resulting power with the previous output and expected power to check whether performance has improved.

## System Architecture

The overall workflow is:

1. **Data acquisition:** Obtain electrical, illuminance, and thermal readings from the dataset or virtual sensors.
2. **Preprocessing:** Clean and organize the input data.
3. **Feature extraction:** Calculate relevant electrical characteristics.
4. **Digital Twin:** Estimate expected power and calculate power deviation.
5. **ML classification:** Predict the panel condition.
6. **Thermal screening:** Independently check for a localized hotspot.
7. **Decision engine:** Recommend monitoring, cleaning, tilt adjustment, or a safety alert.
8. **Communication and verification:** Publish status through MQTT and evaluate simulated recovery.

## Technologies Used

- **Python** — data processing, feature extraction, and system logic
- **NumPy** — numerical computation
- **Scikit-learn** — Machine Learning classification
- **MATLAB `.mat` datasets** — photovoltaic I–V data
- **Wokwi** — ESP32 and actuator simulation
- **ESP32** — simulated IoT controller
- **MQTT** — messaging between system components
- **JSON** — structured telemetry and status information
- **Digital Twin** — expected-power estimation and performance comparison
- **Virtual sensor models** — INA219-style electrical measurements, BH1750-style illuminance, and AMG8833-style thermal-grid readings

## Simulation Results

The software simulation demonstrates the intended decision-making workflow:

- A clean baseline produces low power deviation.
- Early-stage dust does not immediately trigger cleaning.
- Persistent soiling triggers a simulated cleaning action.
- Partial shading generates a tilt-adjustment request.
- A thermal anomaly triggers a safety alert and inhibits cleaning.

In the demonstrated persistent-soiling test case, power increases from **20.9 W to 25.0 W**, a simulated improvement of approximately **19.6% relative to the initial output**.

These are preliminary software-simulation results, not proof of real-world performance or physical cleaning effectiveness. The current poster reports only seven labelled observations, so broad claims about classifier accuracy are not justified.

## Limitations

- The available labelled dataset is small.
- Sensor inputs and actuator behaviour are simulated.
- Real-world sensor calibration and environmental noise have not been fully validated.
- Physical cleaning and real solar-panel tilt control have not been demonstrated.
- The Digital Twin and classification model require further testing using diverse, independently labelled measurements.

## Future Scope

- Collect a larger dataset from physical solar panels.
- Integrate real electrical, illuminance, and thermal sensors.
- Calibrate illuminance-to-irradiance conversion and the Digital Twin.
- Validate the classifier on independent panels and environmental conditions.
- Implement physical cleaning and motorized tilt control with suitable safety mechanisms.
- Develop a live dashboard for monitoring performance and maintenance alerts.

## Project Team

- **Samiksha Saravanan** — [@samikshasaravanan](https://github.com/samikshasaravanan)
- **S B Daksha** - [@daksha0018](https://github.com/daksha0018)
- **Diya S Kamath** — [@diyaskamath](https://github.com/diyaskamath)

**Department:** Computer and Communication Engineering  
**Institution:** Amrita Vishwa Vidyapeetham, Coimbatore

## Conclusion

This project presents a unified approach to intelligent solar panel monitoring by combining Machine Learning, Digital Twin-based power analysis, thermal anomaly screening, and IoT communication. Its main contribution is a condition-aware decision workflow designed to reduce unnecessary cleaning and support early identification of potential panel issues. Further hardware implementation and real-world validation are required before practical deployment.

---

*Developed as an academic Open Laboratory project.*
