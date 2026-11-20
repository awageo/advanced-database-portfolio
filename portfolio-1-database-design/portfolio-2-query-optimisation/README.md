# Portfolio2: Query Optimisation
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

#[Table.txt](https://github.com/user-attachments/files/35177235/Table.txt)
[Status.txt](https://github.com/user-attachments/files/35177233/Status.txt)
[result.txt](https://github.com/user-attachments/files/35177231/result.txt)
