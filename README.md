# advanced-database-portfolio
DROP DATABASE IF EXISTS seph_db;
CREATE DATABASE IF NOT EXISTS seph_db;
USE seph_db;

# InnoDB: The default storage engine in MySQL 8.0. 
# InnoDB is a transaction-safe (ACID-compliant) storage engine 
# InnoDB for MySQL has the function to commit, rollback, and crash-recovery capabilities to protect user data.
# The nature of the database comprises patients, doctors, appointments, prescriptions, and laboratory records

