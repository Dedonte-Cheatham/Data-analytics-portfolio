-- Healthcare Data Analysis Using SQL
-- Example analysis on patient visits / claims-style data

-- Example table assumed:
-- healthcare_visits
-- Columns:
-- patient_id
-- visit_id
-- visit_date
-- age
-- gender
-- diagnosis_category
-- facility
-- county
-- cost
-- length_of_stay

--------------------------------------------------
-- 1. View all records
--------------------------------------------------
SELECT *
FROM healthcare_visits;

--------------------------------------------------
-- 2. Count total visits
--------------------------------------------------
SELECT COUNT(*) AS total_visits
FROM healthcare_visits;

--------------------------------------------------
-- 3. Count unique patients
--------------------------------------------------
SELECT COUNT(DISTINCT patient_id) AS total_unique_patients
FROM healthcare_visits;

--------------------------------------------------
-- 4. Total cost by diagnosis category
--------------------------------------------------
SELECT
diagnosis_category,
COUNT(*) AS visit_count,
ROUND(SUM(cost), 2) AS total_cost,
ROUND(AVG(cost), 2) AS avg_cost
FROM healthcare_visits
GROUP BY diagnosis_category
ORDER BY total_cost DESC;

--------------------------------------------------
-- 5. Average length of stay by diagnosis category
--------------------------------------------------
SELECT
diagnosis_category,
ROUND(AVG(length_of_stay), 2) AS avg_length_of_stay
FROM healthcare_visits
GROUP BY diagnosis_category
ORDER BY avg_length_of_stay DESC;

--------------------------------------------------
-- 6. Visits by gender
--------------------------------------------------
SELECT
gender,
COUNT(*) AS visit_count
FROM healthcare_visits
GROUP BY gender
ORDER BY visit_count DESC;

--------------------------------------------------
-- 7. Visits by age group
--------------------------------------------------
SELECT
CASE
WHEN age < 18 THEN '0-17'
WHEN age BETWEEN 18 AND 34 THEN '18-34'
WHEN age BETWEEN 35 AND 49 THEN '35-49'
WHEN age BETWEEN 50 AND 64 THEN '50-64'
ELSE '65+'
END AS age_group,
COUNT(*) AS visit_count,
ROUND(AVG(cost), 2) AS avg_cost
FROM healthcare_visits
GROUP BY
CASE
WHEN age < 18 THEN '0-17'
WHEN age BETWEEN 18 AND 34 THEN '18-34'
WHEN age BETWEEN 35 AND 49 THEN '35-49'
WHEN age BETWEEN 50 AND 64 THEN '50-64'
ELSE '65+'
END
ORDER BY visit_count DESC;

--------------------------------------------------
-- 8. Top 10 facilities by total cost
--------------------------------------------------
SELECT
facility,
COUNT(*) AS visit_count,
ROUND(SUM(cost), 2) AS total_cost
FROM healthcare_visits
GROUP BY facility
ORDER BY total_cost DESC
LIMIT 10;

--------------------------------------------------
-- 9. County-level utilization summary
--------------------------------------------------
SELECT
county,
COUNT(*) AS total_visits,
COUNT(DISTINCT patient_id) AS unique_patients,
ROUND(SUM(cost), 2) AS total_cost,
ROUND(AVG(cost), 2) AS avg_cost
FROM healthcare_visits
GROUP BY county
ORDER BY total_visits DESC;

--------------------------------------------------
-- 10. Monthly trend in visits
--------------------------------------------------
SELECT
DATE_TRUNC('month', visit_date) AS visit_month,
COUNT(*) AS total_visits,
ROUND(SUM(cost), 2) AS total_cost
FROM healthcare_visits
GROUP BY DATE_TRUNC('month', visit_date)
ORDER BY visit_month;

--------------------------------------------------
-- 11. Highest-cost diagnosis categories
--------------------------------------------------
SELECT
diagnosis_category,
ROUND(SUM(cost), 2) AS total_cost
FROM healthcare_visits
GROUP BY diagnosis_category
ORDER BY total_cost DESC
LIMIT 5;

--------------------------------------------------
-- 12. Patients with multiple visits
--------------------------------------------------
SELECT
patient_id,
COUNT(*) AS visit_count,
ROUND(SUM(cost), 2) AS total_cost
FROM healthcare_visits
GROUP BY patient_id
HAVING COUNT(*) > 1
ORDER BY visit_count DESC, total_cost DESC;

--------------------------------------------------
-- 13. Example data quality check for missing values
--------------------------------------------------
SELECT
SUM(CASE WHEN patient_id IS NULL THEN 1 ELSE 0 END) AS missing_patient_id,
SUM(CASE WHEN visit_date IS NULL THEN 1 ELSE 0 END) AS missing_visit_date,
SUM(CASE WHEN diagnosis_category IS NULL THEN 1 ELSE 0 END) AS missing_diagnosis_category,
SUM(CASE WHEN cost IS NULL THEN 1 ELSE 0 END) AS missing_cost
FROM healthcare_visits;
