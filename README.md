# Face Recognition + RFID Attendance System (Raspberry Pi)

An on-site attendance system designed for field workers: the worker **scans their face** and **taps their RFID card** at the same time. When both match, the system records attendance and generates outputs that make it easier to calculate payroll (daily logs + Excel sheets).

This project is built for **Raspberry Pi** and uses **OpenCV / NumPy + face_recognition** for recognition, plus an **MFRC522 RFID reader** for card verification. :contentReference[oaicite:0]{index=0}

---

## What it does

### ✅ Dual-factor check (Face + Card)
- Reads an RFID card (MFRC522).
- Detects the face via camera and matches it against saved encodings.
- Only accepts when the face matches the card’s owner with a confidence rule (multiple encodings per person). :contentReference[oaicite:1]{index=1}

### ✅ Entry / Exit + Overtime flows
From the main GUI, you can:
- **Take Entry (Giriş al)**
- **Take Exit (Çıkış al)**
- **Overtime Entry / Exit (Ek Mesai)** (requires foreman approval)
- **Add Worker (Kişi ekle)**
- **Remove Worker (Kişi çıkar)** :contentReference[oaicite:2]{index=2}

### ✅ Exports to Excel (and optional Google Sheets)
- Generates a **daily .xlsx** file with worker details + entry/exit times + computed totals. :contentReference[oaicite:3]{index=3}
- Updates a master “Puantaj.xlsx” template (stored on external media path in code). :contentReference[oaicite:4]{index=4}
- Contains code to append to Google Sheets using a service account (credentials JSON in repo). :contentReference[oaicite:5]{index=5}

---

## Hardware

- Raspberry Pi (tested with PiCamera flow)
- **Pi Camera** (or a compatible camera module)
- **MFRC522 RFID reader**
- RFID cards/tags

---

## Repo layout (high level)

- `gui.py` – Full-screen Tkinter “control panel” (entry/exit/add/remove/overtime, foreman toggle). :contentReference[oaicite:6]{index=6}  
- `person_add.py` – Register worker:
  - Writes TC to RFID card
  - Captures 4 photos using `raspistill`
  - Builds/updates `database.json` (encodings + metadata). :contentReference[oaicite:7]{index=7}  
- `person_quit.py` – Remove worker by TC (deletes their 4 encodings & metadata from `database.json`). :contentReference[oaicite:8]{index=8}  
- `pi_face_recognition_main.py` – Entry flow: face + RFID verification, writes daily JSON log. :contentReference[oaicite:9]{index=9}  
- `pi_face_recognition_exit_main.py` – Exit flow: updates exit times and produces Excel exports. :contentReference[oaicite:10]{index=10}  
- `card_reader.py` – Minimal RFID read helper (MFRC522). :contentReference[oaicite:11]{index=11}  

---

## How it works

### 1) Register a worker (Kişi ekle)
- Enter worker info (Name/Surname/TC/Job/Site).
- System asks to scan a card.
- Writes TC to the card.
- Captures 4 images via `raspistill`.
- Extracts face encodings and stores them into `database.json`. :contentReference[oaicite:12]{index=12}

### 2) Take entry (Giriş al)
- Worker scans card + face.
- System finds the worker by card TC, then verifies the face against that worker’s stored encodings.
- Records “scheduled entry time” vs “actual entry time” into a daily JSON file. :contentReference[oaicite:13]{index=13}

### 3) Take exit (Çıkış al)
- Same card + face verification.
- Updates the worker’s exit time in the daily JSON log.
- Generates exports:
  - Daily `.xlsx`
  - Updates master Puantaj sheet :contentReference[oaicite:14]{index=14}
