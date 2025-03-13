# <img src="Images/CyberClassify-logo.png" alt="Logo" width="300"/>
## A carefully crafted tool for classifying and organizing malware datasets.


## 📝 Table of Contents

1. [Introduction](#1-introduction)
2. [Features](#2-features)
3. [Requirements and Installation](#3-requirements-and-installation)
4. [Usage Guide](#4-usage-guide)
5. [Folder Structure](#5-folder-structure)
6. [Contribution Guidelines](#6-contribution-guidelines)
7. [Future Roadmap](#7-future-roadmap)
8. [License](#8-license)
9. [Acknowledgements](#9-acknowledgements)

## 1. Introduction

**CyberClassify** is an advanced malware categorization and dataset management tool designed for cybersecurity researchers, analysts, and dataset creators. It streamlines the process of organizing, classifying, and analyzing malware samples by automating file naming conventions, sorting executables into structured categories, and exporting metadata in a structured format.

Whether you're building a malware dataset, conducting threat analysis, or researching cybersecurity trends, **CyberClassify** simplifies dataset management and ensures consistency in file organization.

![Process involved in classification](/Images/DatasetCollection.png)

### 🔍 Key Capabilities:
- Efficiently categorizes malware into **classes and families** based on naming conventions.
- Automates **file organization and renaming** to maintain dataset integrity.
- Generates structured **CSV reports** for easy analysis of malware distributions.
- Supports **year-wise classification** to track malware records.
- Ensures dataset **cleanliness and accuracy** by removing incomplete or misplaced files.

## 2. Features

✔️ **Automated Malware Categorization** – Organizes malware samples into structured class and family folders.  
✔️ **File Naming Standardization** – Ensures consistent naming conventions for easy identification.  
✔️ **Dataset Cleanup** – Identifies and removes misplaced or incomplete files.  
✔️ **Year-wise Classification** – Tracks malware samples by their year of appearance.  
✔️ **CSV Data Export** – Generates structured CSV reports for easy dataset analysis.  
✔️ **Scalable & Adaptable** – Designed to handle large datasets efficiently.  

CyberClassify simplifies the process of managing malware datasets, making research and analysis more structured and efficient.

## 3. Requirements and Installation  

To use CyberClassify, ensure you have the following:  

- **Python** (Recommended: Latest stable version)  
- **VS Code** or **Jupyter Notebook** for running the scripts 
- **[VirusTotal](https://www.virustotal.com/gui/home/upload) Premium API** for malware classification  
- **[AVClass](https://github.com/malicialab/avclass) (MaliciaLab fork)** – Install using:
```
pip install avclass-malicialab
```
- **Storage Space** – Requirements depend on the size of your dataset

To download Malware:
- **[VirusShare](https://virusshare.com/)**
- **[Malware Bazaar](https://datalake.abuse.ch/malware-bazaar/)**

[![Download](/Images/Download.png)](https://github.com/Saksham128/CyberClassify/raw/f634c70fa88d21f7efa48d619dbfd4b134e9357f/CyberClassify.zip)


## 4. Usage Guide

### 🌍 Overview

This section provides a step-by-step guide on using the various scripts included in CyberClassify. Each script has a specific function in organizing and processing malware datasets. Below, you'll find details on what each script does, how to use it, and what output to expect.

![Flow diagram of classification](/Images/Flowchart.png)
---
### 1. Sorting Windows Executable Files 📊

#### **Purpose:**  
This script scans a specified source folder for Windows executable files (.exe) and moves them to a designated destination folder. Any non-executable files are permanently deleted.

#### **How to Use:**  
1. Set the `source_folder` variable to the directory containing files to be checked.  
2. Set the `destination_folder` variable to the directory where detected executables should be moved.  
3. Run the script. It will:  
   - Identify Windows executable files using the `file` command.  
   - Move executables to the destination folder.  
   - Delete all non-executable files.  

**Note:** Ensure that the `file` utility is available on your system, as it is typically used in Unix-like environments.


#### **Expected Output:**  
- For each executable file found:  
```
Moved 'filename' to 'destination_folder'.
```

- For non-executable files:
```
Deleting 'fileName' permanently.
```

- When the process completes successfully:
```
File check and copying completed.
```
---
### 2. Renaming to MD5 Hash Name ✏️

#### **Purpose:**
This script handles file renaming and cleanup for files downloaded from VirusShare and MalwareBazaar, ensuring they are correctly formatted for analysis.

**VirusShare files:** The script removes the `VirusShare_` prefix, leaving only the MD5 hash as the filename.\
**MalwareBazaar files:** The script renames executable `(.exe)` files to their MD5 hash and deletes all non-executable files.

#### **How to Use:**
1. Set the folder_path or destination_folder variable to the directory containing the downloaded malware samples.  
2. Run the script. It will:
    - Process VirusShare files by stripping the "VirusShare_" prefix.
    - Process MalwareBazaar files by renaming .exe files to their MD5 hash and deleting other file types.
3. After execution, only properly renamed MD5 hash files will remain in the folder.

#### Expected Output:
- For **VirusShare** files:
```
Renamed 'VirusShare_1234567890abcdef' to '1234567890abcdef'
File renaming completed.
```
- For **MalwareBazaar** files:
```
Renamed 'sample.exe' to 'd41d8cd98f00b204e9800998ecf8427e'
Deleted 'sample.dll'
```
---
### 3. Getting JSON report of each file using VirusTotal 📄

#### **Purpose:**  
This script automates the retrieval of **VirusTotal reports** for malware samples using their **MD5 hash filenames**. It distributes API requests between multiple API keys, saves the reports as JSON files, and manages **API rate limits** by handling quota exhaustion.

#### **How to Use:**  
1. **Set the `folder_path` variable** to the directory containing malware sample files.  
2. **Add VirusTotal API keys** to the `VIRUSTOTAL_API_KEYS` list. The script will distribute requests between them.  
3. Run the script, and it will:  
   - Extract the **MD5 hash** from each file's name.  
   - Query **VirusTotal** for a report.  
   - Save the report as a **JSON file** in the specified folder.  
   - **Automatically handle API quota limits** by waiting until the quota resets.  
   - Keep track of processed hashes to avoid redundant requests.

#### **Expected Output:**

- When a report is successfully retrieved:
```
Saved JSON report for 1234567890abcdef
```
- If an API key’s quota is exhausted:
```
API quota for API key [key] is exhausted.
The quota will be restored in X minutes.
```
- If an error occurs:
```
Error occurred for [hash]: [status code] - [error message]
```
- When processing is complete:
```
All files have been processed.
```
---
### 4. Verify from reports and remove any non-executable file 🔍✔️

#### **Purpose:**  
This script ensures that files in the `onlyPrograms` directory are genuine Windows executables by cross-referencing their VirusTotal JSON reports. It removes both the JSON reports and corresponding files that are not identified as "Win32 EXE" files.

#### **How to Use:**  
1. **Set the `reports_folder` variable** to the path containing your VirusTotal JSON reports.
2. **Set the `main_folder` variable** to the path containing your main files (e.g., executables).
3. Run the script; it will:
   - Open each JSON report in the `reports_folder`.
   - Check if the `type_description` attribute is "Win32 EXE".
   - If not, delete both the JSON report and the corresponding file in the `main_folder`.
   - Provide output detailing which files are kept or deleted.

#### **Expected Output:**

- For a file identified as a Win32 EXE:
```
sample1.json is Win32 EXE. Keeping both JSON report and main file.
```
- For a file not identified as a Win32 EXE:
```
sample2.json is not Win32 EXE. Deleting JSON report and main file.
```
---
### 5. Remove any executable with no report found 🗑️

#### **Purpose:**  
This script ensures that only executable files with **VirusTotal reports** remain in the specified directory. It scans a folder containing executables and deletes any file that does not have a corresponding JSON report.

#### **How to Use:**  
1. **Set the `executables_folder` variable** to the path where the executable files are stored.  
2. **Set the `json_reports_folder` variable** to the path where the VirusTotal JSON reports are stored.  
3. Run the script; it will:  
   - Check if each executable file has a corresponding JSON report.  
   - Delete any executable that does **not** have a report.  
   - Print a summary of deleted files.  

#### **Expected Output:**

- When an unreported file is deleted:
```
Deleted: /path/to/executable.exe
```
- When processing is complete:
```
Total unreported files deleted: X
```
---
### 6. Move the latest executables to a different folder. 🚚

#### **Purpose:**  
This script filters files based on their **creation date**. It identifies and moves all executable files and their corresponding **VirusTotal JSON reports**. The timeline can be adjusted as per user requirements.

#### **How to Use:**  
1. **Set the following folder paths:**  
   - `only_programs_folder`: Path where executable files are stored.  
   - `json_reports_folder`: Path where VirusTotal JSON reports are stored.  
   - `new_json_folder`: Destination folder for JSON reports of selected files.  
   - `new_mal_folder`: Destination folder for executable files created within the date range.  
2. Run the script; it will:  
   - Read each JSON report and extract the **creation date**.  
   - If the file was created between **timeline**, move:  
     - The JSON report to `new_json_folder`.  
     - The corresponding executable to `new_mal_folder`.  
   - Print a message for each successfully moved file.  

#### **Expected Output:**

- When a file is moved:
```
Moved sample1.json and corresponding executable to new folders.
```
---
### 7. Check for any empty JSON report 📄✔️

#### **Purpose:**  
This script scans the specified folder for **empty JSON files**, prints their filenames, and **deletes them**. This ensures that only valid JSON reports remain in the dataset.

#### **How to Use:**  
1. **Set the `folder_path` variable** to the directory containing JSON files.  
2. Run the script; it will:  
   - Check all JSON files in the folder.  
   - Identify files that are **empty or invalid**.  
   - Print the names of deleted files.  
   - Delete the empty JSON files from the directory.  

#### **Expected Output:**

- If empty JSON files are found and deleted:
```
Deleted: file1.json
Deleted: file2.json
Total empty JSON files deleted: 2
```
- If no empty JSON files exist:
```
No empty JSON files found in the folder.
```
---
### 8. Check for any JSON report with incorrect format 🔠

#### **Purpose:**  
This script scans the specified folder for **incorrectly formatted JSON files**, prints their filenames, and **deletes them**. This ensures that only valid JSON reports remain in the dataset.

#### **How to Use:**  
1. **Set the `folder_path` variable** to the directory containing JSON files.  
2. Run the script; it will:  
   - Check all JSON files in the folder.  
   - Identify files with **invalid JSON formatting**.  
   - Print the names of deleted files.  
   - Delete the invalid JSON files from the directory.  

#### **Expected Output:**

- If invalid JSON files are found and deleted:
```
Deleted: corrupted1.json
Deleted: corrupted2.json
Total invalid JSON files deleted: 2
```
- If all JSON files are correctly formatted:
```
No files with invalid JSON format found in the folder.
```
---
### 9. Main classifier program 🗂️⚙️

#### Purpose
This script classifies malware samples based on AVClass results. It processes JSON reports, extracts classification labels, and organizes the corresponding executable files into categorized folders. It first converts JSON reports into a **one-liner format** before running AVClass, as AVClass requires this format for proper processing.

#### How to Use
1. Ensure the following directories exist (If not, create them and provide the absolute path to the folders):
   - `new_malware` (Contains executable files)
   - `new_malware_json` (Contains JSON reports)
   - `categorizedMalware`
   - `nonClassMalwares`
   
2. Run the script; it will:
    - Convert JSON Reports: Each report is reformatted into a one-liner JSON.
    - Run AVClass: AVClass processes the formatted JSON and extracts malware classification labels.
    - Organize Executables:
        - If a class label is identified, the file moves to /categorizedMalware/<Class>/<Family>/.
        - If only a family label is found, it moves to /nonClassMalwares/<Family>/.
        - If no valid labels are found, the file remains unclassified (Very less posibility).
        - If AVClass does not provide any class name but only the family name then it goes to `nonClassMalwares`.

3. For Example: The files are named like `backdoor_adwind_2018_1.exe` , where `backdoor` is class name, `adwind` is family name, `2018` is its creation year and `1` is its sequence number. If an executable with same class and family appears again it would get a sequence number `2`

#### **Expected Output:**

- Malware executables are systematically categorized into labeled folders based on AVClass classification.
---
### 10. Delete orphaned JSON reports 📄🗑️

#### **Purpose:**  
This script removes **orphaned JSON reports** that no longer have corresponding executable files. It ensures that the `new_malware_json` folder only contains JSON reports for existing executables. This is usually used if the classification system had stopped mid process, due to which, executables have moved but the reports folder still contains the report.

#### **How to Use:**  
1. **Set the folder paths** in the script:  
   - `json_folder`: Path to the folder containing JSON reports.  
   - `executable_folder`: Path to the folder containing executable files.  

2. **Run the script**, and it will:  
   - Scan all JSON files in `new_malware_json`.  
   - Check if the corresponding executable exists in `new_malware`.  
   - **Delete** JSON files that don’t have matching executables.  
   - Print the names of the deleted files.  

#### **Expected Outcome:**  
- Only JSON files with valid executables remain.  
- Orphaned JSON files are removed.  
- The script prints messages for each deleted file:  
```
Deleted sample1.json 
Deleted sample2.json
```
- Once finished, it prints:  
```
Cleanup complete.
```
---
### 11. Final checks ✔️

### **Purpose:**  
This script **corrects filenames** by adding the missing **class name** to executable files in categorized malware folders. It ensures all filenames follow a consistent format. 
- This step was added as upon scrutinizing the dataset some files were not named correctly. It would not be needed frequently. 

### **How to Use:**  
1. **Set the `folder_path` variable** to the categorized malware directory.  
2. Run the script, and it will:  
   - Scan all executable files in subfolders.  
   - Check if the filename **contains the class name**.  
   - If missing, **add the class name** from the parent folder.  
   - Ensure no duplicate filenames exist by appending a **version number** if needed.  
   - Rename the files accordingly.  

---

### 12. Class and Family count CSV 🗒️

### **Purpose:**  
This script **stores malware classification data** into a **CSV file**, listing malware **classes (categories) and families (subcategories)** along with their respective **file counts**. It provides an organized summary of categorized malware.

### **How to Use:**  
1. **Set the `malware_folder` variable** to the directory containing categorized malware.  
2. Run the script, and it will:  
   - Scan all **class folders** (top-level categories).  
   - Identify **family folders** (subcategories within each class).  
   - Count the number of **executable (.exe) files** in each family.  
   - Store the data in **`malware_categories.csv`**, structured as:  

   | Class/Family | Family_1 | Family_2 | ... | Family_N |
   |-------------|----------|----------|-----|----------|
   | Class_1     | Count    | Count    | ... | Count    |
   | Class_2     | Count    | Count    | ... | Count    |
   | ...         | ...      | ...      | ... | ...      |
   | **Total**   | Sum      | Sum      | ... | Sum      |

### **Expected Outcome:**  
- A CSV file (`malware_categories.csv`) with a **detailed count of malware samples** categorized by **class and family**.  
- The **"Total" row** at the bottom will sum the number of files per family across all classes.  
- Example terminal output:  
```
CSV file 'malware_categories.csv' created successfully.
```
---
### 12.1 Class-Year count CSV 🗒️
### **Purpose:**  
This script **categorizes malware samples year-wise** and stores the **count of executables per class and year** in a **CSV file**. It helps in analyzing malware distribution trends over time.

### **How to Use:**  
1. **Set the `malware_folder` variable** to the directory containing categorized malware.  
2. Run the script, and it will:  
   - Scan all **class folders** (top-level malware categories).  
   - Identify the **year** from filenames (formatted as `class_family_YYYY_count.exe`).  
   - Count the number of executables for each class **per year** (from **2016 to 2023**).  
   - Store the data in **`class_year_counts.csv`**, structured as:  

   | Class  | 2016 | 2017 | ... | 2023 | Total |
   |--------|------|------|-----|------|-------|
   | ClassA |  10  |  15  | ... |  8   |  100  |
   | ClassB |  5   |  20  | ... |  12  |  85   |
   | ...    | ...  | ...  | ... | ...  | ...   |
   | **Total** |  50  |  60  | ... |  40  |  500  |


### **Expected Outcome:**  
- A CSV file (`class_year_counts.csv`) with **year-wise counts of malware samples for each class**.  
- The **"Total" row** at the bottom sums the number of files for each year.  
- Example terminal output:
```
CSV file 'malware_categories.csv' created successfully.
```  
---

### 12.2 Family-Year count CSV 🗒️

### **Purpose:**  
This script **categorizes malware families year-wise** and stores the **count of executables per family and year** in a **CSV file**. It helps in analyzing how different malware families have evolved over time.


### **How to Use:**  
1. **Set the `malware_folder` variable** to the directory containing categorized malware.  
2. Run the script, and it will:  
   - Scan all **family folders** (subcategories within malware classes).  
   - Extract the **year** from filenames (formatted as `class_family_YYYY_count.exe`).  
   - Count the number of executables for each family **per year** (from **2016 to 2023**).  
   - Store the data in **`family_year_counts.csv`**, structured as:  

   | Family  | 2016 | 2017 | ... | 2023 | Total |
   |---------|------|------|-----|------|-------|
   | FamilyA |  10  |  15  | ... |  8   |  100  |
   | FamilyB |  5   |  20  | ... |  12  |  85   |
   | ...     | ...  | ...  | ... | ...  | ...   |
   | **Total** |  50  |  60  | ... |  40  |  500  |


### **Expected Outcome:**  
- A CSV file (`family_year_counts.csv`) with **year-wise counts of malware samples for each family**.  
- The **"Total" row** at the bottom sums the number of files for each year.  
- Example terminal output:  
```
CSV file 'family_year_counts.csv' created successfully.
```
---
## 5. Folder Structure  

When you unzip the `CyberClassify.zip` file, the following folders and files will be available:  

### Folders:  
- `json_reports/` – Contains JSON reports downloaded from VirusTotal.  
- `mixedMalware/` – Stores mixed malware samples extracted from zip files of VirusShare or Malware Bazaar before categorization.  
- `new_malware/` – Holds the malware samples between the timeline specified by the user.  
- `new_malware_json/` – Holds the JSON reports of malware samples between the timeline specified by the user.  
- `only_programs/` – Stores the Windows malware executables sorted from the `mixedMalware` folder.  
- `ProcessedHashBackup/` – Backup of processed hash records for reference.

- ⚠️ Making copy of  `processed_hashes.txt` should done manually after every session.

### Files:  
- `CyberClassify.ipynb` – Jupyter Notebook containing all the scripts for classification.  
- `processed_hashes.txt` – Stores processed hashes to avoid duplicate analysis.  


## 6. Contribution Guidelines  

Contributions to **CyberClassify** are WELCOME. Whether it’s bug fixes, new features, or documentation improvements, your help is appreciated.

## 7. Future Roadmap
🚀 CyberClassify is evolving! Here are some upcoming features and enhancements planned for future versions:

- Major bug fixes and improvement.
- Develop a web-based GUI for easier usability.
- Add support for other files types such as .deb, .apk, .elf etc.
- Resume options for seamless classification.
- Implement logging and error-handling improvements.

## 8. License  
CyberClassify is licensed under the [MIT License](LICENSE), allowing free use, modification, and distribution with attribution. See the LICENSE file for details.  

## 9. Acknowledgements  

We would like to acknowledge the contributions of platforms that provide valuable resources for malware research. Special thanks to:  

- [VirusShare](https://virusshare.com) and [MalwareBazaar](https://bazaar.abuse.ch/) for providing access to malware samples.  
- [VirusTotal](https://www.virustotal.com/) for its API, which aids in malware classification and analysis.  
- [AVClass](https://github.com/malicialab/avclass) – A powerful Python tool for extracting malware family names and categorizing malware based on AV labels.

While these platforms did not directly contribute to the development of CyberClassify, their resources were instrumental in facilitating research and dataset creation.
