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
| Type                       | Variable Description                                                                 |
|----------------------------|--------------------------------------------------------------------------------------|
| **Independent Variable**   | Hospital quality (CMS star rating)                                                  |
| **Dependent Variables**    | 7 Outcomes published by CMS:                                                        |
|                            | - Average ED wait time index                                                        |
|                            | - ED volume                                                                         |
|                            | - % patients leaving before being seen                                              |
|                            | - Head CT results follow-up rate                                                    |
|                            | - Colonoscopy follow-up rate                                                        |
|                            | - Electronic Clinical Quality Index                                                 |
|                            | - Sepsis Care Index                                                                 |
| **Control Variables**      | From ACS and EMS data:                                                              |
|                            | - Total population                                                                  |
|                            | - % over age 65                                                                     |
|                            | - % uninsured or on Medicaid                                                        |
|                            | - % with a bachelor’s degree or higher                                              |
|                            | - % below the poverty line                                                          |
|                            | - % Black or % non-white                                                            |
|                            | - Urban/rural dummy                                                                 |
|                            | - Hospital Ownership                                                                |
|                            | - EMS facility density                                                              |
| **Instrumental Variable (IV)** | Distance to the nearest medical school                                         |

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

