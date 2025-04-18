**LBRP Location based research planner LBRB**: 

Functional & Technical Description:


## 1. System Overview

The Location-Based Research Planner (LBRP) desktop application is a stand-alone Python application designed for offline use in remote field research environments. It helps researchers plan, manage, and document spatial field studies without relying on an internet connection. LBRP is built with Pythons native GUI libraries and spatial data tools.


**Key Features**: 

Offline spatial project creation and editing

Drawing and managing POI/GSI geometries

Assigning predefined sensor gear bundles with locations

Export to GeoJSON and static maps

Minimal SQLite database (5 tables)



**Development**: 

**Git-based version management with test automation**: PyTest, Coverage

**Example Use Case**: 

**A small coral reef field research team arrives at a remote island. With no internet access, they start the LBRP application to**: 

Create a project for Reef Survey Phase 1

Draw Points Of Interest (POIs) for sensor drop-points on a cached base map

Assign temperature and salinity sensors to each POI

Store all geometry and sensor data locally in SQLite

Export the plan to GeoJSON for later integration with GeoServer or QGIS



## 2. Development Environment

**Platform**: Linux (HY Chubbli on VM or Ubuntu)

**Python Packaging**: Poetry with .env for secrets and configs

Git version management

**Testing**: PyTest + Coverage



## 3. SQLite Database Schema (5 Tables)



CREATE TABLE users (

id INTEGER PRIMARY KEY AUTOINCREMENT,

full_name TEXT NOT NULL,

email TEXT UNIQUE NOT NULL,

password TEXT NOT NULL,

role TEXT CHECK (role IN ('principal_researcher', 'field_survey_lead')),

is_active BOOLEAN DEFAULT 1

);

CREATE TABLE projects (

id INTEGER PRIMARY KEY AUTOINCREMENT,

title TEXT NOT NULL,

description TEXT,

created_by INTEGER REFERENCES users(id),

created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);

CREATE TABLE features (

id INTEGER PRIMARY KEY AUTOINCREMENT,

project_id INTEGER REFERENCES projects(id),

type TEXT CHECK (type IN ('POI', 'GSI')),

name TEXT,

geometry TEXT NOT NULL, -- GeoJSON encoded

notes TEXT

);

CREATE TABLE sensors (

id INTEGER PRIMARY KEY AUTOINCREMENT,

feature_id INTEGER REFERENCES features(id),

sensor_type TEXT,

gear_bundle TEXT,

depth_meters REAL,

notes TEXT,

calibrated BOOLEAN DEFAULT 0

);

CREATE TABLE sync_log (

id INTEGER PRIMARY KEY AUTOINCREMENT,

project_id INTEGER REFERENCES projects(id),

remote_path TEXT,

local_path TEXT,

synced BOOLEAN DEFAULT 0,

timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP

);



## 4. Tkinter GUI Layout (wireframes)

**Main Tabs**: 

**Project**: Create/select project, assign lead

**Map**: Display base image with overlay geometries

**Features**: Draw sensor (POI) and sensor grid (GSI) and store locally

**Sensors**: Assign sensors to features

**Sync**: Preview or export layer files to disk or GeoServer



## 5. Sample Data (SQL Insert)



INSERT INTO users (full_name, email, password, role) VALUES

('Dr. Carol Smith', 'coral@example.com', 'hashedpassword', 'principal_researcher'),

('Lee Diver', 'lee@example.com', 'hashedpassword2', 'field_survey_lead');



INSERT INTO projects (title, description, created_by) VALUES

('Lagoon Phase 1', 'Baseline study of reef ecosystem response to temperature anomalies.', 1);



INSERT INTO features (project_id, type, name, geometry, notes) VALUES

(1, 'POI', 'Thermal Probe 1', '{"type":"Point","coordinates":[25.41,34.22]}', 'Near reef slope');



INSERT INTO sensors (feature_id, sensor_type, gear_bundle, depth_meters, notes) VALUES

(1, 'temperature', 'Thermal Pack A', 2.0, 'daily measurement');

