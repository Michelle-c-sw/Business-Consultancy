# SQL - Database - Project Management

## Introduction
This project provides a SQL-based database for management of accounting company to manage company's daily operations. The database consists of five core tables: 'Client List','Employee List', 'Case List', 'Billing List','Income Transaction List'. It supports case management, case assignments, querying capabilities and status update.

## Database Schema
![alt text](image.png)

### Create Tables
'''SQL'''
CREATE TABLE IF NOT EXISTS client_list (
    client_id VARCHAR(20) PRIMARY KEY,
    client_name VARCHAR(50) NOT NULL,
    business_registration_no_or_hkid VARCHAR(12) NOT NULL,
    principal_activity VARCHAR(50) NOT NULL,
    accounting_services BOOLEAN,
    accounting_services_frequency VARCHAR(20),
    audit_liaison_services BOOLEAN,
    company_secretarial_services BOOLEAN,
    registered_office_address_services BOOLEAN,
    payroll_services BOOLEAN,
    tax_services VARCHAR(50) NOT NULL,
    address VARCHAR(300) NOT NULL,
    contact_name VARCHAR(50) NOT NULL,
    position VARCHAR(50) NOT NULL,
    phone_no VARCHAR(20) NOT NULL,
    email VARCHAR(100) NOT NULL,
    create_at DATE NOT NULL,
    active BOOLEAN DEFAULT TRUE,
    remarks VARCHAR(100)
);

CREATE TABLE IF NOT EXISTS employee_list (
    staff_id VARCHAR(20) PRIMARY KEY,
    staff_name_english_name VARCHAR(50) NOT NULL,
    staff_name_chinese_name VARCHAR(50),
    department VARCHAR(50) NOT NULL,
    position VARCHAR(50) NOT NULL,
    phone_no VARCHAR(20) NOT NULL,
    email VARCHAR(100) NOT NULL,
    hire_date DATE NOT NULL,
    termination_date DATE,
    salary DECIMAL(10,2) NOT NULL,
    active BOOLEAN DEFAULT TRUE
);

CREATE TABLE IF NOT EXISTS job_list (
    job_id VARCHAR(20) PRIMARY KEY,
    client_id INTEGER NOT NULL,
    client_name VARCHAR(50) NOT NULL,
    service_provided VARCHAR(50) NOT NULL,
    priority VARCHAR(50) NOT NULL,
    staff_id VARCHAR(20) NOT NULL,
    job_status VARCHAR(50) NOT NULL,
    start_date DATE NOT NULL,
    deadline DATE NOT NULL,
    remarks VARCHAR(100)
    FOREIGN KEY (client_id) REFERENCES client_list (client_id),
    FOREIGN KEY (staff_id) REFERENCES employee_list(staff_id)
);

CREATE TABLE IF NOT EXISTS billing_list (
    invoice_id VARCHAR(20) PRIMARY KEY,
    job_id VARCHAR(20) NOT NULL,
    client_id INTEGER NOT NULL,
    client_name VARCHAR(50) NOT NULL,
    description VARCHAR(50) NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    issue_date DATE NOT NULL,
    due_date DATE NOT NULL,
    billing_status VARCHAR(50) NOT NULL,
    remarks VARCHAR(100),
    FOREIGN KEY (job_id) REFERENCES job_list (job_id),
    FOREIGN KEY (client_id) REFERENCES client_list (client_id)
);

CREATE TABLE IF NOT EXISTS income_transactions_list (
    receive_id VARCHAR(20) PRIMARY KEY,
    invoice_id VARCHAR(20) NOT NULL,
    amount DECIMAL(10,2) NOT NULL,
    received_date DATE NOT NULL,
    method VARCHAR(50) NOT NULL,
    reference VARCHAR(50) NOT NULL,
    remarks VARCHAR(100),
    FOREIGN KEY (invoice_id) REFERENCES billing_list (invoice_id)
)

### Insert Sample Data
INSERT INTO client_list
VALUES ('0001','Great Rich Success Limited 大展鴻圖有限公司','88888888','General Trading',1,'Yearly',1,1,1,1,'Profits Tax & Employer Tax Return','Room 8801-8803, 8/F, Happy Commercial Building, 1 Queensway, Central, Hong Kong','KO Goo Jik','Director','+852 23456789','kgj@greatrichsuccess.com','01-01-2025',1,'N/A'),
       ('0002','Billion Profits Limited','69999999','General Trading',1,'Yearly',1,1,1,1,'Profits Tax & Employer Tax Return','Room 2808-2810, 28/F, Happy Commercial Building, 1 Queensway, Central, Hong Kong','Chan Tai Yat','Director','+852 87654321','cty@billionprofits.com','01-01-2025',1,'N/A'),
       ('0003','LI Bei Shu','M666666(6)','N/A',1,'Yearly',0,0,0,0,'Individual Tax','Room 708, 7/F, Lucky Commercial Building, 2 Nathan Road, Kowloon, Hong Kong','Li Bei Shu','N/A','+852 12345678','bsli@qualtysec.com','01-01-2025',1,'N/A');

INSERT INTO employee_list
VALUES ('HK0001','LEE Tai Fu', '李大富','Accounting','Senior Associate I','98765432','tflee@abcaccountingltd.com','01-01-2025',NULL,25000.00,1),
       ('HK0002','YU Mei Yan', '余美人','Company Secretary','Senior Associate I','61234578','myyu@abcaccountingltd.com','01-01-2025',NULL,25000.00,1),
       ('HK0003','CHAN Tai Man', '陳大文','Tax','Senior Associate I','53168986','tmchan@abcaccountingltd.com','01-01-2025',NULL,25000.00,1);

INSERT INTO job_list
VALUES ('T250001', '0003','LI Bei Shu','Individual Tax Return Filing 2024/25','Normal','HK0003','CHAN Tai Man','Completed','15-04-2025','15-05-2025','N/A'), 
       ('A250001', '0001','Great Rich Success Limited 大展鴻圖有限公司','Accounting for the year ended 31 March 2025','Urgent','HK0001','LEE Tai Fu','WIP','01-11-2025','15-12-2025','N/A'), 
       ('C250001', '0001','Great Rich Success Limited 大展鴻圖有限公司','NAR1 2025','Normal','HK0002','YU Mei Yan','WIP','15-11-2025','12-01-2026','N/A'),
       ('C250002', '0002','Billion Profits Limited','ND2A','Normal','HK0002','YU Mei Yan','WIP','15-11-2025','30-11-2025','N/A');

INSERT INTO billing_list
VALUES ('INV250001','T250001','0003','LI Bei Shu','Individual Tax Return Filing 2024/25',2000.00,'05-05-2025','05-06-2025','Issued','N/A');

INSERT INTO income_transactions_list
VALUES ('R-250001','INV250001',2000.00,'20-05-2025','Cheque','HSBC Cheque#980005','N/A');

## Run Queries in SQL environment (e.g. MySQL, PostgreSQL, SQLite)

### Example Queries 1 - View the case of particular staff
SELECT * FROM job_list
WHERE staff_name = 'YU Mei Yan';

### Example Queries 2 - Track the settlement status of the billing
SELECT * FROM billing_list
JOIN income_transaction_list
ON billing_list.invoice_id = income_transaction_list.invoice_id;

### Example Queries 3 - Update the settlement status 
UPDATE billing_list
SET billing_status = 'Settled'
WHERE invoice_id = 'INV250001';

### Example Queries 4 - Remove canceled case
DELETE FROM job_list
WHERE job_id = 'C250002';