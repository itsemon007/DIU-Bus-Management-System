# DIU Bus Management System

A database-driven, web-ready system to organize university transportation—covering routes, buses, seating, schedules, and maintenance.

> Built for the Database Management System course (Course Code: 0612-303) at Dhaka International University.

---

## ✨ Key Features

- **Route & Bus Assignment:** Assign which bus serves which route and store distance.
- **Seating & Priority:** Capacity-aware seat allocation with priority for teachers.
- **Schedules:** Day-wise arrival times per bus & route.
- **Maintenance Logs:** Costs, issue descriptions, and next due dates (weak entity tied to `Bus`).
- **Role Views:** Students, teachers, and admins access the data they need.

---

## 🧱 Data Model (ER → Relational)


**Bus**  
- `bus_id`, `bus_number`, `bus_type`, `capacity`, `registration_number`, `bus_name`

**Route**  
- `route_id`, `route_name`, `start_point`, `end_point`, `total_distance`, `estimate_time`, `bus_id`

**Student**  
- `student_id`, `s_name`, `roll_number`, `department`, `phone_number`, `email`, `address`, `pickup_point`, `bus_id`, `route_id`

**Teacher**  
- `teacher_id`, `t_name`, `department`, `phone_number`, `email`, `address`, `pickup_point`, `bus_id`, `route_id`

**Bus_Schedule**  
- `schedule_id`, `bus_id`, `route_id` , `arrival_time`, `day_of_week`

**Maintenance** (Weak)  
- `maintenance_id` , `bus_id` , `maintenance_date`, `maintenance_type`, `issue_description`, `cost`, `next_due_date`

---

## 🗄️ SQL Schema (MySQL)

> You can run this in a fresh MySQL schema named `diu_bms`.

```sql
CREATE DATABASE IF NOT EXISTS diu_bms;
USE diu_bms;

-- BUS
CREATE TABLE Bus (
  bus_id INT AUTO_INCREMENT PRIMARY KEY,
  bus_number VARCHAR(20) NOT NULL,
  bus_type ENUM('AC','Non-AC') NOT NULL DEFAULT 'Non-AC',
  capacity INT NOT NULL CHECK (capacity > 0),
  registration_number VARCHAR(50) UNIQUE NOT NULL,
  bus_name VARCHAR(100) NOT NULL
);

-- ROUTE
CREATE TABLE Route (
  route_id INT AUTO_INCREMENT PRIMARY KEY,
  route_name VARCHAR(100) NOT NULL,
  start_point VARCHAR(100) NOT NULL,
  end_point VARCHAR(100) NOT NULL,
  total_distance DECIMAL(6,2) NOT NULL,
  estimate_time TIME NOT NULL,
  bus_id INT,
  CONSTRAINT fk_route_bus
    FOREIGN KEY (bus_id) REFERENCES Bus(bus_id)
    ON DELETE SET NULL ON UPDATE CASCADE
);

-- STUDENT
CREATE TABLE Student (
  student_id INT AUTO_INCREMENT PRIMARY KEY,
  s_name VARCHAR(100) NOT NULL,
  roll_number VARCHAR(50) UNIQUE NOT NULL,
  department VARCHAR(100) NOT NULL,
  phone_number VARCHAR(30),
  email VARCHAR(120),
  address VARCHAR(255),
  pickup_point VARCHAR(120),
  bus_id INT,
  route_id INT,
  CONSTRAINT fk_student_bus
    FOREIGN KEY (bus_id) REFERENCES Bus(bus_id)
    ON DELETE SET NULL ON UPDATE CASCADE,
  CONSTRAINT fk_student_route
    FOREIGN KEY (route_id) REFERENCES Route(route_id)
    ON DELETE SET NULL ON UPDATE CASCADE
);

-- TEACHER
CREATE TABLE Teacher (
  teacher_id INT AUTO_INCREMENT PRIMARY KEY,
  t_name VARCHAR(100) NOT NULL,
  department VARCHAR(100) NOT NULL,
  phone_number VARCHAR(30),
  email VARCHAR(120),
  address VARCHAR(255),
  pickup_point VARCHAR(120),
  bus_id INT,
  route_id INT,
  CONSTRAINT fk_teacher_bus
    FOREIGN KEY (bus_id) REFERENCES Bus(bus_id)
    ON DELETE SET NULL ON UPDATE CASCADE,
  CONSTRAINT fk_teacher_route
    FOREIGN KEY (route_id) REFERENCES Route(route_id)
    ON DELETE SET NULL ON UPDATE CASCADE
);

-- BUS_SCHEDULE
CREATE TABLE Bus_Schedule (
  schedule_id INT AUTO_INCREMENT PRIMARY KEY,
  bus_id INT NOT NULL,
  route_id INT NOT NULL,
  arrival_time TIME NOT NULL,
  day_of_week ENUM('Saturday','Sunday','Monday','Tuesday','Wednesday','Thursday','Friday') NOT NULL,
  CONSTRAINT fk_sched_bus
    FOREIGN KEY (bus_id) REFERENCES Bus(bus_id)
    ON DELETE CASCADE ON UPDATE CASCADE,
  CONSTRAINT fk_sched_route
    FOREIGN KEY (route_id) REFERENCES Route(route_id)
    ON DELETE CASCADE ON UPDATE CASCADE
);

-- MAINTENANCE (Weak entity depends on BUS)
CREATE TABLE Maintenance (
  maintenance_id INT AUTO_INCREMENT PRIMARY KEY,
  bus_id INT NOT NULL,
  maintenance_date DATE NOT NULL,
  maintenance_type VARCHAR(100) NOT NULL,
  issue_description TEXT,
  cost DECIMAL(10,2) NOT NULL DEFAULT 0,
  next_due_date DATE,
  CONSTRAINT fk_maint_bus
    FOREIGN KEY (bus_id) REFERENCES Bus(bus_id)
    ON DELETE CASCADE ON UPDATE CASCADE
);

-- Useful indexes
CREATE INDEX idx_student_route ON Student(route_id);
CREATE INDEX idx_teacher_route ON Teacher(route_id);
CREATE INDEX idx_schedule_day_bus ON Bus_Schedule(day_of_week, bus_id);


## 👥 Contributors:

This project was developed by:

1. MD Emon Sarkar - @itsemon007

2. Farjana Hoque Remo - @farzanahoque430

3. Showrov Shahriar Pran - @ShowrovShahariar

4. Sidratul Montaha Ayshi - @Ayshi32
