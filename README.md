from flask import Flask, request
import csv, os
from werkzeug.utils import secure_filename

app = Flask(__name__)
UPLOAD_FOLDER = 'uploads'
app.config['UPLOAD_FOLDER'] = UPLOAD_FOLDER

if not os.path.exists(UPLOAD_FOLDER):
    os.makedirs(UPLOAD_FOLDER)

@app.route('/api/new_driver_full', methods=['POST'])
def new_driver_full():
    form = request.form
    photo = request.files['photo']
    
    photo_filename = secure_filename(photo.filename)
    photo_path = os.path.join(app.config['UPLOAD_FOLDER'], photo_filename)
    photo.save(photo_path)

    data_row = [
        form.get('driver_id'), form.get('name'), photo_filename, form.get('birthdate'),
        form.get('age'), form.get('gender'), form.get('cast'), form.get('address'),
        form.get('mobile'), form.get('alt_mobile'), form.get('email'),
        form.get('bank_account'), form.get('aadhar'), form.get('salary'),
        form.get('license_no'), form.get('license_start'), form.get('license_end'),
        form.get('education'), form.get('performance'), form.get('experience'),
        form.get('working_hours'), form.get('shifts'), form.get('leaves'), form.get('attendance'),
        form.get('depot')
    ]

    headers = [
        "Driver_ID", "Name", "Photo", "Birthdate", "Age", "Gender", "Cast", "Address",
        "Mobile", "Alt_Mobile", "Email", "Bank_Account", "Aadhar", "Salary",
        "License_No", "License_Start", "License_End", "Education", "Performance",
        "Experience", "Working_Hours", "Shifts", "Leaves", "Attendance", "Depot"
    ]

    filename = 'drivers_full.csv'
    file_exists = os.path.exists(filename)

    with open(filename, 'a', newline='') as f:
        writer = csv.writer(f)
        if not file_exists or os.stat(filename).st_size == 0:
            writer.writerow(headers)
        writer.writerow(data_row)

    return f"Driver {form.get('name')} added successfully with ID: {form.get('driver_id')}"
