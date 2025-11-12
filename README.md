# End-to-End Analysis of Ecobici CDMX: Data Pipeline and Business Intelligence

<img width="3300" height="440" alt="0ecobici-gob_logo" src="https://github.com/user-attachments/assets/808b274f-f0d8-47f3-a5d2-76cecdc02f7b" />


## 1. 🎯 About the Project

This project is a **portfolio analysis** that simulates a real business case. The objective was to build a complete data pipeline (ETL) and a Business Intelligence dashboard to analyze the behavior of users of the Ecobici system in Mexico City.

The analysis aims to answer key business questions to support decision-making in **Marketing** and **Operations**.

---

## 2. 🛠️ Tools

* **Data Extraction:** Python (libs: `requests`, `pandas`, `beautifulsoup4`)
* **Storage (Data Warehouse):** `MySQL Server`
* **Transformation (ETL):** `Python (Pandas)` and `Power Query (M)`
* **Modeling and Visualization:** `Power BI`
* **Analysis (Business Logic):** `DAX`

---

## 3. ⚙️ Data Pipeline Architecture

This project follows a defined flow, from raw data to interactive analysis.

`EcoBici Website CDMX(Open Source)` ➔ `🐍 Python (Scraping and Transformation)` ➔ `🗄️ MySQL (Storage)` ➔ `📊 Power BI (Modeling, DAX, and Visualization)`

---

## 4. 📋 Project Stages

### Stage 1: Extraction & Transformation (Python)

* **Web Scraping:**  `BeautifulSoup` and `requests` were used to retrieve data from the Ecobici Open Data portal, automatically identifying and downloading the trip CSV files.
* **Consolidation:** `pandas` was used to read, consolidate, and merge multiple CSV files (representing 3 months of data) into a single DataFrame containing approximately 5 million rows.
* **Feature Processing:** A transformation in `pandas` was performed to combine separate `Fecha_Retiro` and `Hora_Retiro` columns into a single `timestamp` field. From this, the main business metric was calculated.: **`Duracion_Minutos`**.

### Stage 2: Carga (MySQL)

Instead of relying on a local CSV file, the transformed data was loaded into a MySQL server to simulate a real, robust, and scalable production environment.

* `SQLAlchemy` and `PyMySQL` were used to connect Python to the Database
* Approximately 5 million rows were loaded into a fact table (`viajes`) using `df.to_sql()`.

### Stage 3: Modeling and Cleaning (Power BI / Power Query)

* **Multi-Source Connection:** he Power BI model connects to two distinct data sources
    1.  The `MySQL` database (for the fact table `viajes`).
    2.  A **live API JSON** (for the dimension table `Estaciones`), ensuring that station names and locations are always up to date.
* **Cleaning (Power Query):**
    * Handled `null`, `(Blank)` and `?`values in key columns (`Genero_Usuario`).
    * Used **"Column From Examples"** to get a clean key (`ID_Estacion`) from messy text in the JSON data.
    * Managed data type `Errors` using **"Replace Errors"**.
* **Data Model:** A relational (1:N) data model was built, linking the `Estaciones` dimension table with the `viajes` fact table through **two relationships**: one for departures and another for arrivals.

<img width="1003" height="473" alt="data_model_1" src="https://github.com/user-attachments/assets/5cd8de96-5aa0-44a4-a97b-f1d948322c4a" />


### Stage 4: Analysis (DAX)

To answer key business questions, several DAX measures were created.

* **`USERELATIONSHIP`:** The measure`[Total Arribos]` was created to activate the model’s inactive relationship, allowing for comparisons between departures and arrivals at the same station.
* **`CALCULATE`:** Used to isolate customer segments. For example, the measure`[Duración Promedio (Identificados)]`was created to calculate the average excluding the `NR` (Not Registered) group, helping quantify bias.
* **Key Business Metric:** The measure `[Flujo Neto] = [Total Viajes] - [Total Arribos]` was developed to identify logistical imbalances at each station.

---

## 5. 💡 Findings and Dashboards

### Page 1: Executive Summary (The "What", "Who", and "When")

This dashboard provides an overview of key KPIs and user segmentation.

<img width="1280" height="715" alt="dashboard_pag_1" src="https://github.com/user-attachments/assets/674421a6-a44a-4ff8-9ea6-88c832af0cb7" />


* **Finding #1 (Segmentation):** Three user groups were identified. The M (Male) group is the largest. However, the average trip duration in minutes was affected when including the 'NR' (Not Registered) segment. This introduces noise into the analysis since the users gender segmentation is incomplete. Initially, we assume these records could correspond to tourist users, as their registration process is typically simpler and shorter.
* **Finding #2 (Identification):** A partial conclusion was later reached that 'NR' users are likely tourists, based on the stations where this user category takes the most trips — primarily located near tourist areas.

### Page 2: Geographic Analysis (The "Where")

This dashboard focuses on Operations and Logistics questions.

<img width="1279" height="717" alt="dashboard_pag_2" src="https://github.com/user-attachments/assets/d1d3b7cd-0283-4569-849f-5549529b8d46" />
<img width="1279" height="715" alt="dashboard_pag_3" src="https://github.com/user-attachments/assets/8fafe85a-379d-444e-94f5-74006cc7b8f8" />


* **Finding #3 (Hypothesis: "Tourist" vs. "General"):**
    * `M` and `F` ("General") users have their most popular departure stations in residential areas or work hubs (`J72 Jesus Garcia`, `Dr. Mariano Azuela`).
    * The `NR` ("Tourist") group has its most popular stations in **tourist zones** (`Circuito Mahatma Gandhi-Reforma`, `Sonora-Amsterdam`).
* **Finding #4 (Net Flow):** The "Imbalance Flow" map visually identifies stations that **lose bicycles** (require replenishment) and those that accumulate bicycles (require collection), providing an actionable tool for the logistics team.
---

## 6. 📂 Repository Structure

* **`.ipynb`:** Contains all the Python code for scraping, transformation, and loading.
* **`.pbit`:** This file contains the complete data model, Power Query transformations (ETL), and dashboard design. It does not include imported data to keep the file lightweight and suitable for GitHub.
* **`.csv`:** This file contains only the first million records that were stored in the MySQL database.

## 7. 👤 Author

* **[Andric Zárate]**
* **LinkedIn:** `[(https://www.linkedin.com/in/zarateandric/)]`
