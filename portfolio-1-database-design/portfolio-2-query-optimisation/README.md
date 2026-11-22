# Portfolio2: Query Optimisation
Indexing & Optimization Evidence
* Elimination of using filesort:
As the idx_appt_status_date creates an index in order of (status, appointment_date), the WHERE clause on status = 'Completed' with ORDER BY appointment_date DESC will result in reading the leaves of the B+Tree in reverse order. Thus, using filesort is eliminated.
* Covering Index Usage (Using index):
The idx_presc_appt_med and idx_lab_appt_status contain all the columns used in the SELECT clause (medication_name, duration_days, test_type, status). The InnoDB engine performs the LEFT JOIN operation using the secondary index pages only, halving the disk I/O by skipping the search for the primary key clustered index lookup.
* Search Argumentable Predicate:
Changing the predicate function YEAR(appointment_date) = 2026 to appointment_date >= '2026-01-01' AND appointment_date < '2027-01-01' preserves predicate SARGable (Search ARGument-able), thus allowing the optimizer to use range scan instead of full table scan (ALL).
* Duplicate Index Avoidance:
MySQL utilizes these as single-column foreign key lookups, avoiding duplicate single-column indexes that slow down INSERT and UPDATE operations.

USE seshc_db;

# 1. CREATE INDEXES (the create index syntax is hashed since the table has already exits therefore havinng a duplicate key
#CREATE INDEX idx_patient_active_name ON patients (is_active, last_name, first_name);
#CREATE INDEX idx_appt_status_date    ON appointments (status, appointment_date, patient_id, doctor_id);
#CREATE INDEX idx_presc_appt_med      ON prescriptions (appointment_id, medication_name, duration_days);
#CREATE INDEX idx_lab_appt_status     ON lab_records (appointment_id, status, test_type);


# 2. FETCH COMPLETED 2026 APPOINTMENTS
SELECT 
    p.patient_id,
    CONCAT_WS(' ', p.first_name, p.last_name) AS patient_name,
    CONCAT('Dr. ', d.last_name, ' (', d.specialization, ')') AS attending_doctor,
    a.appointment_date,
    a.status AS appointment_status,
    pr.medication_name,
    pr.dosage,
    pr.duration_days,
    lr.test_type,
    lr.status AS lab_status
FROM appointments a
JOIN patients p         ON a.patient_id = p.patient_id
JOIN doctors d          ON a.doctor_id = d.doctor_id
LEFT JOIN prescriptions pr ON a.appointment_id = pr.appointment_id
LEFT JOIN lab_records lr   ON a.appointment_id = lr.appointment_id
WHERE p.is_active = TRUE
  AND a.status = 'Completed'
  AND a.appointment_date >= '2026-01-01'
  AND a.appointment_date <  '2027-01-01'
ORDER BY a.appointment_date DESC;

 3. UPDATE TABLE STATS & CHECK INDEXES
ANALYZE TABLE patients, doctors, appointments, prescriptions, lab_records;
SHOW INDEX FROM appointments;

[Optimisation_query.sql](https://github.com/user-attachments/files/35244647/Optimisation_query.sql)
<img width="1917" height="1077" alt="Opt_Result_3" src="https://github.com/user-attachments/assets/99bafcb7-ab77-4054-bdbf-41a63bbeaeb4" />
<img width="1912" height="1077" alt="Opt_Result_2" src="https://github.com/user-attachments/assets/de6de819-caf2-4bdf-b6ea-9fadcbb43e16" />
<img width="1912" height="1077" alt="Opt_Result_1" src="https://github.com/user-attachments/assets/4345bcf7-bd7b-4445-b4dc-5a30ccba8244" />

