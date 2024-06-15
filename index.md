---
title: "Home"
layout: default
permalink: /all-python-flask/
--- 

[Home](index.md)

Project Structure
```python
flask_app/
│
├── app.py
├── db.sqlite
├── requirements.txt
├── static/
│   └── styles.css
└── templates/
    ├── register.html
    └── login.html
```

requirements.txt
```python
Flask==2.0.2
Flask-SQLAlchemy==3.0.1
requests==2.26.0
passlib==1.7.4
python-dotenv==0.19.1
```

app.py
```python
from flask import Flask, request, jsonify
import requests

app = Flask(__name__)

# FastAPI endpoint base URL
FASTAPI_BASE_URL = "http://localhost:8000"

# Store the JWT token
jwt_token = None

# Route to login and get the JWT token from FastAPI
@app.route('/login', methods=['POST'])
def login():
    global jwt_token
    credentials = request.json
    try:
        response = requests.post(f"{FASTAPI_BASE_URL}/auth/login", json=credentials)
        response.raise_for_status()
        jwt_token = response.json().get("access_token")
        return jsonify({"access_token": jwt_token}), 200
    except requests.exceptions.RequestException as e:
        return jsonify({"error": str(e)}), 500

# Helper function to add authorization header
def get_headers():
    if jwt_token:
        return {"Authorization": f"Bearer {jwt_token}"}
    return {}

# Route to get all emails from FastAPI
@app.route('/emails', methods=['GET'])
def get_emails():
    try:
        response = requests.get(f"{FASTAPI_BASE_URL}/email/", headers=get_headers())
        response.raise_for_status()
        emails = response.json()
        return jsonify(emails), 200
    except requests.exceptions.RequestException as e:
        return jsonify({"error": str(e)}), 500

# Route to add a new email using FastAPI
@app.route('/emails', methods=['POST'])
def add_email():
    data = request.json
    try:
        response = requests.post(f"{FASTAPI_BASE_URL}/email/", json=data, headers=get_headers())
        response.raise_for_status()
        email = response.json()
        return jsonify(email), 201
    except requests.exceptions.RequestException as e:
        return jsonify({"error": str(e)}), 500

# Route to update an email by ID using FastAPI
@app.route('/emails/<int:email_id>', methods=['PUT'])
def update_email(email_id):
    data = request.json
    try:
        response = requests.put(f"{FASTAPI_BASE_URL}/email/{email_id}", json=data, headers=get_headers())
        response.raise_for_status()
        email = response.json()
        return jsonify(email), 200
    except requests.exceptions.RequestException as e:
        return jsonify({"error": str(e)}), 500

# Route to delete an email by ID using FastAPI
@app.route('/emails/<int:email_id>', methods=['DELETE'])
def delete_email(email_id):
    try:
        response = requests.delete(f"{FASTAPI_BASE_URL}/email/{email_id}", headers=get_headers())
        response.raise_for_status()
        return '', 204
    except requests.exceptions.RequestException as e:
        return jsonify({"error": str(e)}), 500

if __name__ == '__main__':
    app.run(debug=True, port=5000)
```

style.css
```
/* styles.css */

@tailwind base;
@tailwind components;
@tailwind utilities;
```
templates/register.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Registration</title>
    <link href="{{ url_for('static', filename='styles.css') }}" rel="stylesheet">
</head>
<body class="bg-gray-100 h-screen flex items-center justify-center">
    <div class="bg-white p-6 rounded-lg shadow-md max-w-md w-full">
        <h1 class="text-2xl font-semibold mb-6">User Registration</h1>
        <form method="POST">
            <div class="mb-4">
                <label for="username" class="block text-gray-700">Username:</label>
                <input type="text" id="username" name="username" required class="border-gray-300 border w-full p-2 rounded-md focus:outline-none focus:ring focus:border-blue-300">
            </div>
            
            <div class="mb-4">
                <label for="password" class="block text-gray-700">Password:</label>
                <input type="password" id="password" name="password" required class="border-gray-300 border w-full p-2 rounded-md focus:outline-none focus:ring focus:border-blue-300">
            </div>
            
            <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600 transition-colors">Register</button>
        </form>
    </div>
</body>
</html>
```

templates/login.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>User Login</title>
    <link href="{{ url_for('static', filename='styles.css') }}" rel="stylesheet">
</head>
<body class="bg-gray-100 h-screen flex items-center justify-center">
    <div class="bg-white p-6 rounded-lg shadow-md max-w-md w-full">
        <h1 class="text-2xl font-semibold mb-6">User Login</h1>
        <form method="POST">
            <div class="mb-4">
                <label for="username" class="block text-gray-700">Username:</label>
                <input type="text" id="username" name="username" required class="border-gray-300 border w-full p-2 rounded-md focus:outline-none focus:ring focus:border-blue-300">
            </div>
            
            <div class="mb-4">
                <label for="password" class="block text-gray-700">Password:</label>
                <input type="password" id="password" name="password" required class="border-gray-300 border w-full p-2 rounded-md focus:outline-none focus:ring focus:border-blue-300">
            </div>
            
            <button type="submit" class="bg-blue-500 text-white px-4 py-2 rounded-md hover:bg-blue-600 transition-colors">Login</button>
        </form>
    </div>
</body>
</html>
```

pip install -r requirements.txt
npx tailwindcss build styles.css -o static/styles.css

python app.py

