# Portfolio 1: Database Design
# First, create the database schema
CREATE DATABASE IF NOT EXISTS seshc_db;
USE seshc_db;

# 1. Patients Table
CREATE TABLE IF NOT EXISTS patients (
    patient_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    dob DATE NOT NULL,
    gender ENUM('Male', 'Female', 'Other') NOT NULL,
    blood_group ENUM('A+','A-','B+','B-','AB+','AB-','O+','O-') NOT NULL,
    phone VARCHAR(20) UNIQUE NOT NULL,
    address TEXT,
    -- UPDATED: Soft-delete flag so historical medical records are never accidentally wiped, culled from Gemini ai to enhance the database
    is_active BOOLEAN NOT NULL DEFAULT TRUE,
    INDEX idx_patient_name (last_name, first_name)
) ENGINE=InnoDB;

# 2. Attending Physician (Doctors) Table
CREATE TABLE IF NOT EXISTS doctors (
    doctor_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    specialization VARCHAR(100) NOT NULL,
    license_no VARCHAR(30) NOT NULL UNIQUE,
    ## UPDATED: Added UNIQUE constraint to prevent duplicate doctor contact numbers, culled from Gemini ai to enhance the database
    phone VARCHAR(20) NOT NULL UNIQUE
) ENGINE=InnoDB;

# 3. Schedule Appointments Table
CREATE TABLE IF NOT EXISTS appointments (
    appointment_id INT AUTO_INCREMENT PRIMARY KEY,
    patient_id INT NOT NULL,
    doctor_id INT NOT NULL,
    appointment_date DATETIME NOT NULL,
    status ENUM('Scheduled', 'Completed', 'Cancelled', 'No-Show') DEFAULT 'Scheduled',
    diagnosis_notes TEXT,
    -- UPDATED: Changed to ON DELETE RESTRICT to protect medical audit trails culled from Gemini ai to enhance the database
    CONSTRAINT fk_appt_patient FOREIGN KEY (patient_id) 
        REFERENCES patients(patient_id) ON DELETE RESTRICT,
    CONSTRAINT fk_appt_doctor FOREIGN KEY (doctor_id) 
        REFERENCES doctors(doctor_id) ON DELETE RESTRICT,
    CONSTRAINT uq_doctor_slot UNIQUE (doctor_id, appointment_date),
    -- UPDATED: Prevents a patient from being double-booked at the same time, culled from Gemini ai to enhance the database
    CONSTRAINT uq_patient_slot UNIQUE (patient_id, appointment_date),
    INDEX idx_appt_date (appointment_date)
) ENGINE=InnoDB;

# 4. Medical Prescriptions Table
CREATE TABLE IF NOT EXISTS prescriptions (
    prescription_id INT AUTO_INCREMENT PRIMARY KEY,
    appointment_id INT NOT NULL,
    medication_name VARCHAR(100) NOT NULL,
    dosage VARCHAR(50) NOT NULL,
    frequency VARCHAR(50) NOT NULL,
    ## UPDATED: Added NOT NULL so a duration must be explicitly provided, culled from Gemini ai to enhance the database
    duration_days INT NOT NULL CHECK (duration_days > 0),
    CONSTRAINT fk_presc_appt FOREIGN KEY (appointment_id) 
        REFERENCES appointments(appointment_id) ON DELETE CASCADE
) ENGINE=InnoDB;

# 5. Laboratory Records Table
CREATE TABLE IF NOT EXISTS lab_records (
    lab_id INT AUTO_INCREMENT PRIMARY KEY,
    appointment_id INT NOT NULL,
    test_type VARCHAR(100) NOT NULL,
    # UPDATED: Allowed NULL so pending lab tests can be logged before results arrive, culled from Gemini ai to enhance the database
    result_summary TEXT NULL,
    test_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status ENUM('Pending', 'Completed') DEFAULT 'Pending',
    CONSTRAINT fk_lab_appt FOREIGN KEY (appointment_id) 
        REFERENCES appointments(appointment_id) ON DELETE CASCADE
) ENGINE=InnoDB;


# InnoDB: The default storage engine in MySQL 8.0. 
# InnoDB is a transaction-safe (ACID-compliant) storage engine 
# InnoDB for MySQL has the function to commit, rollback, and crash-recovery capabilities to protect user data.
# The nature of the database comprises patients, doctors, appointments, prescriptions, and laboratory records

[P1_DDM_Schema.sql](https://github.com/user-attachments/files/35244680/P1_DDM_Schema.sql)

<img width="866" height="587" alt="Awa_Enyinnaya_EER_Diagram" src="https://github.com/user-attachments/assets/96a9cfd4-be76-4db7-9a07-240b2fd21f0f" />

