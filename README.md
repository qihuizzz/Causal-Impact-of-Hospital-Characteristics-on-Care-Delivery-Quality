# Causal Impact of Hospital Characteristics on Care Delivery Quality
## An Instrumental Variable Approach with Spatial Analysis
**Group Members:** Qi Zhao, Qihui Zhang, Jakub Buds

<img src="https://github.com/user-attachments/assets/c0e836c3-7bf2-4b79-941f-75edeb68908d" width="400"/>

# 1. Problem Statement
Hospital quality has been identified as a major determinant of patient outcomes and a central focus of U.S. health policy reforms (Institute of Medicine, 2001; Zuckerman et al., 2016). It directly impacts clinical outcomes, patient satisfaction, and resource allocation. However, estimating the true effect of hospital quality on healthcare outcomes is challenging due to potential endogeneity and the spatial uncertainty of hospital service areas. Therefore, **our key question is: Does higher hospital quality causally improve patient outcomes?**

# 2. Methodology
We use an instrumental variable (IV) strategy. Specifically, the distance to the nearest medical school, to causally estimate the impact of hospital quality on multiple hospital-level outcomes. To account for spatial uncertainty in defining hospital service areas, we implement and compare three geographic delineation methods: buffer zones, Voronoi polygons, and NHS-defined Hospital Service Areas (HSA).

**Specifically, we will implement three service-zone definitions to test robustness:**

**(1) Buffer-based Service Area (Main Model)**
- For each hospital, create 10-miles/15-miles/20-miles/25-miles/30-miles buffer zones
- Overlay with census & EMS data to construct hospital-level controls(Main analysis at hospital level)
- Robustness: Repeat on rural/urban hospitals only
  
**(2) Voronoi-based Service Area (Robustness Model)**
- Construct Voronoi polygons: each hospital "owns" the area nearest to it
- Overlay with census & EMS data (Also at the hospital level)
- Useful to address spatial distortion (e.g. overlapping buffers or MAUP)
  
**(3) Hospital Service Areas (HSA) – Zone-Level Analysis**
- Use NHS-defined HSAs (each may contain multiple hospitals)
- Aggregate hospital quality/outcomes to the HSA level
- Match with HSA-level population data
- This version will inform policy-level recommendations:
    - Which HSAs have high health demand but few or poor-performing hospitals?

**Model Setup**
**Variables Overview**

| **Independent Variable**               | **Dependent Variables (CMS Outcomes)**                                                                                                                                                                                                                                   | **Control Variables (ACS & EMS)**                                                                                                                                                                                                                                      | **Instrumental Variable (IV)**                          |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------|
| Hospital quality (CMS star rating)     | - Average ED wait time index  <br> - ED volume  <br> - % patients leaving before being seen  <br> - Head CT results follow-up rate  <br> - Colonoscopy follow-up rate  <br> - Electronic Clinical Quality Index  <br> - Sepsis Care Index                            | - Total population  <br> - % over age 65  <br> - % uninsured or on Medicaid  <br> - % with a bachelor’s degree or higher  <br> - % below the poverty line  <br> - % Black or % non-white  <br> - Urban/rural dummy  <br> - Hospital Ownership  <br> - EMS facility density | Distance to the nearest medical school                  |

<sub>
**Notes**  
1. CMS – The Centers for Medicare & Medicaid Services  
2. ED – Emergency Department  
3. CT – Computed Tomography Scan  
4. LCME – Liaison Committee on Medical Education  
</sub>

**Variable Dictionary**

| Variable                | Description                                                                                  | Source / Construction                                                                                                     | Interpretation                                                                                       |
|-------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| **Independent Variable: Hospital Quality** |                                                                                              |                                                                                                                           |                                                                                                       |
| Hospital_overall_rating | Composite CMS rating of overall hospital performance                                          | Aggregated from four domains: mortality, safety, readmission, patient experience                                          | Ranges from 1 to 4 stars; higher scores indicate better overall quality                              |
| **Dependent Variable: Hospital Outcome**   |                                                                                              |                                                                                                                           |                                                                                                       |
| ED_Wait_Index           | Composite index of Emergency Department (ED) wait times                                       | Standardized combination of OP_18b (arrival-to-provider time) and OP_18c (arrival-to-departure time)                      | A lower score indicates longer wait times. May reflect hospital crowding or inefficiency.            |
| ED_volumn_score         | Categorical ED volume indicator                                                              | Derived from EDV (coded from Low, Medium, High, Very High to 1–4)                                                         | A proxy for patient demand. Higher scores indicate higher ED visit volume.                           |
| Head_Score              | Rate of timely return of head CT results in the ED                                           | Based on OP_29 or similar CMS measure                                                                                     | A higher percentage indicates faster imaging diagnostics and more efficient ED operations.           |
| Lwbs_Rate               | Percentage of patients who left the ED without being seen                                    | From CMS measure OP_22                                                                                                     | Higher rates suggest excessive wait times and reflect poor patient throughput or satisfaction.       |
| Colo_followup_rate      | Follow-up rate after a positive colonoscopy screening                                        | Based on OP_33 or a related quality measure                                                                               | A proxy for outpatient care coordination. Higher rates reflect better continuity of care.            |
| Sepsis_Care_Index       | Composite score of sepsis management quality                                                 | Combined from 5 sepsis-related measures (SEP_1, SEP_SH_3HR, SEP_SH_6HR, SEV_SEP_3HR, SEV_SEP_6HR), averaged and standardized | Higher scores indicate better adherence to clinical protocols for time-sensitive emergency care.     |
| ECQM_Index_Std          | Composite index of electronic clinical quality measures (eCQMs)                              | Standardized average of 12 CMS-reported eCQM components                                                                   | A proxy for digital infrastructure and clinical process quality. Higher values indicate better digital monitoring and compliance. |

### 📊 Data Sources
#### 🏥 Hospital Level Data (CMS)
- [Hospital General Information](https://data.cms.gov/provider-data/dataset/xubh-q36u)
- [Timely and Effective Care – Hospital](https://data.cms.gov/provider-data/dataset/yv7e-xc69#data-table)
#### 🗺 Geographic & Demographic Data
- [American Census Data](https://www.census.gov/)
- [U.S. Census Cartographic Boundary Files (Shapefiles)](https://www.census.gov/geographies/mapping-files/time-series/geo/carto-boundary-file.html)
- [Health Service Areas (HSA) Shapefile - SEER](https://seer.cancer.gov/seerstat/variables/countyattribs/hsa.html)
#### 🚨 Emergency Facility Data
- [Fire and EMS Stations – HIFLD GeoPlatform](https://hifld-geoplatform.hub.arcgis.com/datasets/geoplatform::fire-and-emergency-medical-service-ems-stations/about)
#### 🏫 Medical School Locations
- [LCME Accredited MD Programs in the United States](https://lcme.org/directory/accredited-u-s-programs/?utm_source=chatgpt.com)
- [AACOM: U.S. Colleges of Osteopathic Medicine](https://www.aacom.org/become-a-doctor/prepare-for-medical-school/us-colleges-of-osteopathic-medicine?utm_source=chatgpt.com)

### 🧷 Tools and Technologies
- **Data Processing, Extraction, Analysis**: Python, Pandas, NumPy, PyPDF2
- **Geospatial Analysis:** GeoPandas, ArcGIS
- **Data Visualization:** Matplotlib, Seaborn
- **Data Management:** MySQL

# 3. Data Preprocessing
**Initial Cleaning**
We focus exclusively on hospitals located in the contiguous United States, as healthcare systems, accessibility, and demographic characteristics in Alaska, Hawaii, and Puerto Rico differ substantially from those on the mainland. As a result, we excluded a total of 11 hospitals from Alaska, Hawaii, and Puerto Rico, leaving 2,671 hospitals in our final analysis sample.

<img src="https://github.com/user-attachments/assets/449c779b-111b-4955-8f4e-f07d09d61e19" width="400"/>

**Outliers**
- Following conventional practice and in order to retain more observations, we excluded extreme values for the standardized outcome variables using the mean ± 3 standard deviations rule.
- Note: Given that measures such as the colonoscopy follow-up rate may naturally exhibit extreme values, we did not apply this exclusion to other variables. A total of 64 observations were removed as a result.

<img src="https://github.com/user-attachments/assets/9de65b88-6e11-4d90-9a34-b196066f484f" width="400"/>

**Multicollinearity**
- In assessing multicollinearity, we generated a heatmap for all variables, as shown in the figure on the right.
- Given the high correlation between total population and EMS count, as well as between percent white and percent Black, we included only total population (instead of EMS count) and percent white (instead of percent Black) in the final model to avoid multicollinearity.
<img src="https://github.com/user-attachments/assets/c2c2e137-f4d1-4ce4-a739-fc2f4da75842" width="400"/>

# 4. Data Analysis
## 4.1. Buffer Zone Analysis
**Distribution of Hospital-level Variables (30 Miles Buffer)**
<img src="https://github.com/user-attachments/assets/23188ad5-a587-4da5-bed6-b59370f99b0e" width="600"/>

**Comparison of service coverage of urban and rural hospitals at different radii in Illinois**
<img src="https://github.com/user-attachments/assets/1cd1fe02-bbd8-4d76-a645-c724609c6d0c" width="600"/>

**Matrix of Hospital Quality and Demand at the County Level**
<img src="https://github.com/user-attachments/assets/95d285a2-3b7d-4d18-81c2-5c0baaad0e8a" width="600"/>

**IV Results on Different Buffers (10-30 miles)**
- To assess how proximity to medical schools affects hospital quality, we examine the strength of our instrument—distance to the nearest medical school—under different buffer sizes. As the service area expands, the instrument’s explanatory power increases, suggesting broader physician networks and academic influence.
#### 📈 Regression Results for ED_Wait_Index under Different Buffer Zones

| Buffer Zone | Coefficient | Std. Err. | P-value |
|-------------|-------------|-----------|---------|
| 10 miles    | **2.4430**  | 1.6644    | 0.1422  |
| 15 miles    | **2.1795**  | 1.3053    | 0.0950  |
| 20 miles    | **1.8126**  | 0.9451    | 0.0551  |
| 25 miles    | **1.8963**  | 1.0639    | 0.0747  |
| 30 miles    | **1.8451**  | 1.0111    | 0.0680  |

> **Note:** The table summarizes the estimated coefficients of the hospital quality variable on the ED_Wait_Index across different geographic buffer zones (10 to 30 miles). A positive coefficient indicates longer ED wait times associated with higher CMS hospital quality ratings, though the results vary in statistical significance.

<img src="https://github.com/user-attachments/assets/5b34d2d2-1550-41f8-a408-8190b0487e97" width="300"/>
- OLS regression underestimates the impact of hospital quality on emergency department waiting times. IV regression results suggest that high-quality hospitals significantly reduce patients' average waiting time in the emergency department, although there is some uncertainty in the estimates

**IV Results on Different Buffers: Let’s focus on 30 miles buffer**
- The effect is particularly pronounced in **urban hospitals**, where close integration between teaching institutions and hospitals is more common. In contrast, **rural hospitals exhibit no significant association**, which may reflect geographic isolation, weaker institutional ties, or distinct healthcare delivery models. This urban–rural divergence highlights the importance of context in evaluating policy levers like academic infrastructure as drivers of care quality.

<img src="https://github.com/user-attachments/assets/983a6521-2b58-4885-97d2-43a1368cb112" width="400"/>

**Geographic Distribution of Regression Residuals for Wait Time Index**
<img src="https://github.com/user-attachments/assets/031e0c5b-92c7-4577-85de-227eb0cb4841" width="600"/>

## 4.2. Voronoi Polygons Analysis
**EMS & Hospital Points**
- We use **Voronoi polygons** to define each hospital’s primary service area based on spatial proximity—assigning geographic regions to the nearest hospital. This method approximates real-world patient flow by capturing **catchment areas** without relying on administrative boundaries.
<img src="https://github.com/user-attachments/assets/25addd23-d44e-4527-b99a-ed9e1f9df09c" width="600"/>

**Key Variable Distributions among Voronoi Polygons**
<img src="https://github.com/user-attachments/assets/486c292c-6e84-443a-8c0d-5a9c14d485e0" width="600"/>

**IV Results on Voronoi Hospital Areas (full model, control for rural/urban indicator)**

#### 📊 Regression Results (Full Model)

| Outcome               | Coefficient | Std. Err. | P-value |
|-----------------------|-------------|-----------|---------|
| **ED_Wait_Index**     | 6.2382      | 19.7909   | 0.7526  |
| ED_Volume_Score       | 9.8482      | 24.6474   | 0.6895  |
| Head_Score            | 24.8397     | 58.0508   | 0.6687  |
| Lwbs_Rate             | -0.0583     | 4.8554    | 0.9904  |
| Colo_followup_rate    | -225.17     | 2129.524  | 0.9158  |
| Sepsis_Care_Index     | -0.1457     | 2.9771    | 0.9610  |
| ECQM_Index_Std        | 7.4768      | 31.5696   | 0.8128  |

> **Note:** This table presents the estimated impact of hospital quality on multiple care delivery outcomes. None of the coefficients are statistically significant (p > 0.05), suggesting limited evidence of a strong linear relationship in this specification.

- The results are much different and entirely insignificant. Why?
- The method (Voronoi Polygons):
  - assign each hospital the area closest to it
  - non-overlapping boundaries
- The regression: 
  - join census data via intersecting tracts
  - different groups of controls
  - may not be as inclusive of controlled effects in hospital-dense areas

**IV Results on Voronoi Hospital Areas (Rural vs Urban)**
- Splitting the regressions between the urban and rural groups reveals generally greater p-values and SE’s in the urban group
  - Instrumental variable becomes less relevant in urban areas due to higher density of medical schools
- Urban hospitals have more competition, so scores will vary greatly from rural hospitals that may be the only choice

## 4.3. NHS-defined Hospital Service Areas (HSA) Analysis
Hospital Service Areas (HSAs) are geographic regions—typically counties or clusters of counties—where residents primarily receive hospital care. Defined by the National Center for Health Statistics, HSAs reflect actual patient flow patterns, offering a more behaviorally grounded unit of analysis.

Compared to buffer zones or Voronoi polygons, which rely purely on geographic proximity, HSAs provide a policy-relevant perspective by capturing how people actually use hospital services. While their boundaries are broader, HSAs complement our hospital-level IV analysis by better reflecting real-world healthcare demand and system load.

- After matching, we get 949 HSAs in Contiguous United States.

<img src="https://github.com/user-attachments/assets/70e1c97a-3218-4d65-88a4-a0e3ac236a67" width="600"/>

<img src="https://github.com/user-attachments/assets/135f45aa-5fce-463f-88ad-34b100cb8b96" width="600"/>

<img src="https://github.com/user-attachments/assets/f5821e23-c1c0-4a0b-947a-61326a5f13a8" width="600"/>

**How is the Result? Also Insignificant …**
- At the HSA level, **our IV analysis yields insignificant results across all hospital outcomes**. This lack of significance suggests that when patient flow is accounted for, distance to medical schools fails to serve as a valid instrument, suggesting that the instrument's effectiveness is compromised at broader geographic aggregations where patients have more hospital choices and sorting patterns become more complex.
- We also test on urban and rural data subsets, but the results are similar.

#### 📊 Regression Results (Model with T-statistics)

| Outcome              | Coefficient | Std. Err. | T-stat   | P-value |
|----------------------|-------------|-----------|----------|---------|
| **ED_Wait_Index**    | 0.24990     | 0.04230   | 5.90700  | 0.2656  |
| ED_Volume_Score      | 0.08204     | 0.03093   | 2.65200  | 0.5410  |
| Head_Score           | 0.00558     | 0.00196   | 2.84800  | 0.4677  |
| Lwbs_Rate            | 0.12870     | 0.01759   | 7.31400  | 0.5989  |
| Colo_followup_rate   | 0.01162     | 0.00250   | 4.62900  | 0.4935  |
| Sepsis_Care_Index    | 0.18870     | 0.03746   | 5.03800  | 0.8698  |
| ECQM_Index_Std       | 0.14930     | 0.03510   | 4.25500  | 0.4335  |

> **Note:** This table reports coefficient estimates, standard errors, t-statistics, and p-values for the effect of hospital quality on various care outcomes. None of the variables are statistically significant at conventional levels.

# 5. Conclusion & Policy Recommendations
## 5.1. Conclusion
-Our analysis reveals several key findings regarding the impact of hospital quality, as instrumented by proximity to medical schools, on hospital outcomes across different spatial delineation methods:
  - 1. **Buffer zones**: Using buffer radii from 10 to 30 miles, we observe consistently positive and significant effects from the 15-mile buffer onwards, particularly among urban hospitals. 
  - 2. **Voronoi polygons**: Results are not statistically significant, suggesting this method fails to capture the relevant mechanisms. 
  - 3. **Hospital Service Areas (HSAs)**: Analysis yields insignificant results, indicating that the instrument loses validity when actual patient flow patterns are incorporated. 

- These findings suggest that the "talent effect" of medical schools operates within geographically constrained boundaries. The instrument's effectiveness relies on strict geographic proximity rather than realistic patient flow patterns. 

- When accounting for actual patient movement (HSAs and Voronoi methods), the instrument becomes ineffective, indicating that medical school effects operate through direct geographic proximity rather than broader healthcare delivery networks that patients navigate.

## 5.2. Policy Recommendation
- **Target geographically-focused interventions around existing medical schools**, as talent effects operate within localized boundaries rather than broader service networks. 
- **Prioritize medical school placement and expansion in underserved areas**, since proximity-based quality improvements are spatially constrained and don't extend through patient flow networks. 
- **Use buffer-based planning for hospital quality initiatives** rather than patient flow-based methods, as geographic proximity better captures where medical school talent effects actually operate.

# 6. Future Directions
- Incorporate time-series data to assess how proximity to medical schools affects hospital quality trends over time
- Differentiate by teaching hospital status to explore whether effects are stronger in academic-affiliated institutions
- Examine specialty-specific outcomes (e.g., emergency, surgical) to uncover domain-specific impacts
- Use spatial distribution mapping to identify underserved areas with weak academic–clinical linkages
- Consider instrument heterogeneity by testing alternative IVs or multi-instrument strategies

# Reference
Institute of Medicine (IOM, now the National Academy of Medicine). (2001). Crossing the Quality Chasm: A New Health System for the 21st Century.

Zuckerman et al. (2016). “Readmissions, Observation, and the Hospital Readmissions Reduction Program.” New England Journal of Medicine, 374(16), 1544-1551.

The White House. (2025, February). Establishing the President’s Make America Healthy Again Commission. https://www.whitehouse.gov/presidential-actions/2025/02/establishing-the-presidents-make-america-healthy-again-commission/

Department of Health and Human Services. (2025, February 6). Private equity in healthcare: Report highlights concerns and calls for oversight. The Guardian. https://www.theguardian.com/us-news/2025/feb/06/private-equity-healthcare

