healthcaredatabsemanagement
code: // Import necessary modules const express = require('express'); const mongoose = require('mongoose'); const bodyParser = require('body-parser'); const app = express();

Import libraries
import os from datetime import datetime
import bcrypt import pymongo from flask import Flask, request, jsonify from flask_jwt_extended import JWTManager, create_access_token

// Connect to MongoDB mongoose.connect('mongodb://localhost/healthcareDB', { useNewUrlParser: true, useUnifiedTopology: true }) .then(() => console.log('MongoDB Connected')) .catch(err => console.log(err));

// Create a Patient schema and model const patientSchema = new mongoose.Schema({ firstName: String, lastName: String, dob: Date, gender: String, medicalHistory: String, });

const Patient = mongoose.model('Patient', patientSchema);

// Middleware app.use(bodyParser.json());

// API endpoints for patient management app.post('/api/patients', async (req, res) => { try { const patient = new Patient(req.body); await patient.save(); res.status(201).json(patient); } catch (error) { res.status(500).json({ error: 'Could not create patient' }); } });

app.get('/api/patients', async (req, res) => { try { const patients = await Patient.find(); res.json(patients); } catch (error) { res.status(500).json({ error: 'Could not retrieve patients' }); } });

// Start the server const port = process.env.PORT || 3000; app.listen(port, () => { console.log(Server is running on port ${port}); });

Initialize app
app = Flask(name) app.config['MONGO_URI'] = os.environ['MONGO_URI'] # MongoDB connection string mongo = PyMongo(app)

Config JWT
app.config['JWT_SECRET_KEY'] = os.environ['JWT_SECRET']
jwt = JWTManager(app)

Database collections
users = mongo.db.users patients = mongo.db.patients

Password encryption
def encrypt_password(password): return bcrypt.hashpw(password.encode('utf8'), bcrypt.gensalt())

JWT required decorator
def token_required(f): @wraps(f) def wrapper(*args, **kwargs): token = request.args.get('token') try: jwt.decode(token, app.config['JWT_SECRET_KEY']) return f(*args, **kwargs) except: return jsonify({'error': 'Invalid token'}), 401 return wrapper

Register new user
@app.route('/users', methods=['POST']) def register_user(): username = request.get_json()['username'] password = request.get_json()['password']

Validate if user exists
if users.find_one({"username": username}): return jsonify({"error": "Username already exists"})

Encrypt and save password
encrypted_password = encrypt_password(password) users.insert_one({ "username": username, "password": encrypted_password })

return jsonify({"message": "Registration successful"})

Login user
@app.route('/login', methods=['POST']) def login_user(): username = request.get_json()['username'] password = request.get_json()['password']

user = users.find_one({"username": username})

Validate password
if user and bcrypt.checkpw(password.encode('utf8'), user['password']): access_token = create_access_token(identity=username)
return jsonify(access_token=access_token) else: return jsonify({"error": "Invalid credentials"})

Patient CRUD routes below
@app.route('/patients', methods=['POST'])
@token_required def add_patient():

Access JWT identity
current_user = get_jwt_identity()

Validate user is doctor
if current_user['role'] != 'doctor': return jsonify({"error": "Unauthorized"})

Add patient logic
name = request.get_json()['name']

etc
return jsonify({"message": "Patient added"})

if name == 'main': app.run() `''
