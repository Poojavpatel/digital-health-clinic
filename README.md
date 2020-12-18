## Database schema and API outline for a digital tele-health clinic

App Requirements
* Doctors can ADD or UPDATE fixed timings for fixed days.
* Patients can view available appointments in the next 7 days.
* Patients can view available appointments based on problems faced (eg-tooth ache).
* Patients can search doctors of various specializations eg - Cardiologist
* Searching based on locations
* send SMS when appointment is booked,confirmed,cancelled etc
* Maintain invoices/pdf of payments made
* Record feedback/reviews/comments from patients

---
## Database schema
   
Models required in this project will be
1. Doctor - Store basic info, qualifications and contact information about doctors
2. Patient - Store basic and contact information about patients
3. Specialization - Store medical specializations like orthopedic, neurologist, dentist, etc
4. Hospital - Stores data of Hospitals/Clinics where doctors work
5. **Schedule** - **A join table of Doctor and Hospital where details of one doctor in one hospital are saved**  
since a doctor has different time avalability at different hospitals or at his clinic,
and different clinics have different location it is better it as a different model
5. Appointments - Stores details of appointments with people, time and place

### Doctors Model
Basic info, qualifications and contact information about doctors

```javascript
{
 "name": "Doctor",
 "base": "User",
 "properties": {
   "id": {
     "type": "string",
     "id": true,
     "required": true
   },
   "name": {
     "type": "string",
     "required": true
   },
   "mobile": {
     "type": "string",
     "required": true,
     "unique": true
   },
   "email": {
     "type": "string",
     "required": false,
     "unique": true
   },
   "overallRating":{
     "type": "number",   // overall rating of the doctor eg - 3.7/5 stars
   },
   "firstConsultationFee": {
     "type":"number",
   },
   "professional_statement": {  // detail about the doctor's qualification, specialisation, experience as entered by the doctor     
     "type": "string",
   },
   "practicing_from ": { // date when doctor started practicing mediciene
     "type": "date",
     "required": false,
   }
   "qualification": { // array of all qualification objects of a doctor
      "type":"array",
      "properties":{
        "name" : {"type":"String"},   // name of the certification or degree
        "institute" : {"type":"String"},
        "qualification_year": {"type": "date"},
      },
   }
 },
 "relations": {
   "specializations": {  // A doctor can have many specializations like gynecologist and endocrinologist
     "type": "hasMany",
     "model": "Specialization",
     "foreignKey": "doctorId",
   },
   "hospitals": {  // A doctor can work in multiple hospitals/clinics
     "type": "hasMany",
     "model": "Hospital",
     "foreignKey": "doctorId",
   },
   "schedules": {  // A doctor can work in multiple hospitals/clinics
     "type": "hasMany",
     "model": "Schedule",
     "foreignKey": "doctorId",
   },
 }
}
```

### Patients Model
Basic and contact information about patients

```javascript
{
 "name": "Patient",
 "base": "User",
 "properties": {
   "id": {
     "type": "string",
     "id": true,
     "required": true
   },
   "name": {
     "type": "string",
     "required": true
   },
   "mobile": {
     "type": "string",
     "required": true,
     "unique": true
   },
   "email": {
     "type": "string",
     "required": false,
     "unique": true
   },
}
```

### Specialization Model
Store medical specializations like orthopedic, neurologist, dentist, etc

```javascript
{
 "name": "Specialization",
 "properties": {
   "id": {
     "type": "string",
     "id": true,
     "required": true
   },
   "doctorId": {
     "type": "string",
     "required": true
   },
   "name": {  // eg - neurologist
     "type": "string",
     "required": true,
   },
   "experience": {    // experience in a specialization in months
     "type": "number",
   }
}
```

### Hospital Model
Hospitals and Clinics where doctors work

```javascript
{
 "name": "Hospital",
 "properties": {
   "id": {
     "type": "string",
     "id": true,
     "required": true
   },
   "doctorId": {
     "type": "string",
     "required": true
   },
   "status":{
     "type":"string"     // ACTIVE, CLOSED, etc 
   },
   "address": {
     "type": "string",
   },
   "pincode":{
     "type": "string",
     "required": true
   },
   "city": {
     "type": "string",
     "required": true,
   },
   "country": {
     "type": "string",
     "required": false,
   },
   "startDate": {
     "type":"date"
   }
   "relations": {
    "doctors": {  // Many doctors work in a hospital
      "type": "hasMany",
      "model": "Doctor",
    },
   }
}
```
### Schedule Model
**A join table of Doctor and Hospital where details of one doctor in one hospital are saved**  
since a doctor has different time avalability at different hospitals or at his clinic,
and different clinics have different location it is better it as a different model

```javascript
{
 "name": "Schedule",
 "properties": {
   "id": {
     "type": "string",
     "id": true,
     "required": true
   },
   "doctorId": {
     "type": "string",
     "required": true
   },
   "hospitalId": {
     "type": "string",
     "required": true,
   },
   "firstConsultationFee":{
     "type": "number",
   },
   "status":{
     "type":"string"     // AVAILABLE, UNAVAILABLE, etc
   },
   "timeSlots":[
     {
      "start": {
        "dateTime": "2020-09-01T00:30:00+02:00"
      },
      "end": {
        "dateTime": "2020-09-01T01:00:00+02:00"
      }
    }
   ]
}
```

### Appointment Model

```javascript
{
 "name": "Appointment",
 "properties": {
   "id": {
     "type": "string",
     "id": true,
     "required": true
   },
   "doctorId": {
     "type": "string",
     "required": true
   },
   "patientId": {
     "type": "string",
     "required": true,
   },
   "hospitalId": {
     "type": "string",
   },
   "details": {
     "type": "string",
     "required": true
   },
   "appointmentDate": {  // day of the appointment
     "type": "date",
   },
   "bookingDate": {   // date when appointment was booked
     "type": "date",
   },
   "bookedBy": {
     "type": "string",
   },
   "probableStartTime": {
     "type": "date",
   },
   "probableEndTime": {
     "type": "date",
   },
   "startTime": {
     "type": "date",
   },
   "endTime": {
     "type": "date",
   },
   "status":{
     "type": "string",   // BOOKED, ONGOING, CANCELLED, RESCHEDULED, INQUIRY
   },
   "modifiedOn":{
     "type": "date",
   },
   "modifiedBy":{
     "type": "string",
   },
   "feedback": {
     "type": "string",
   },
}
```

   
* A model for `Location` can be created and can be added instead of seperate locations   
* A model `SmsLog` can be added which holds status of all sms sent to patient and doctor after every stage   
Some properties would be - status, body, recipentId, error etc

#### SQL can be used for tables with fixed columns and relations eg-patients, and when ACID properties are required
#### Mongodb should be used for scalability and for models whose schema may need to change

---
## API outline

### Patients

* Returns a list of all patients.   
Add the offset and limit parameters to control the page start and size   
Add city parameter to filter by city
Response contains array of all patients
```javascript
GET /api/patients/?skip=0&limit=10
```


* Creates a new patient object and returns it
Request will contain name,mobile,email
```javascript
POST /api/patients
```

* Returns a patient object.
```javascript
GET /api/patients​/{id}
```

* Updates a patient object
```javascript
PUT ​/api/patients​/{id}
```


* Delete a patient
```javascript
DELETE /api/patients​/{id}
```

* get all apointments of a patient
```javascript
GET /api/patient/{id}/appointments
```


* get details of an appointment of a patient
```javascript
Get /api/patient/{id}/appointments/{id}
```
     
        
### Doctors
* CRUD API endpoints simillar to Patients
* `GET /api/doctors`    
search can be based on location,specialization,experience
* get list of all hospitals the doctor works at
```javascript
Get /api/doctor/{id}/hospitals/
```
* get schedule related details of a doctor
```javascript
Get /api/doctor/{id}/schedules/
``` 


### Hospitals
* CRUD API endpoints simillar to Patients
* get list of all doctors who work at a hospital
```javascript
Get /api/hospital/{id}/doctors
```

### Schedule

* Get all available slots of doctors in next 7 days
```javascript
Get /api/schedule/
```

response will contain slots like
```javascript
{
  "from": "2020-09-01",
  "to": "2020-09-02",
  "days": {
    "2020-09-01": {
      "slots": [
        {
          "start": {
            "dateTime": "2020-09-01T00:00:00+02:00"
          },
          "end": {
            "dateTime": "2020-09-01T00:30:00+02:00"
          }
        },
        {
          "start": {
            "dateTime": "2020-09-01T00:30:00+02:00"
          },
          "end": {
            "dateTime": "2020-09-01T01:00:00+02:00"
          }
        },
        {
          "start": {
            "dateTime": "2020-09-01T01:00:00+02:00"
          },
          "end": {
            "dateTime": "2020-09-01T01:30:00+02:00"
          }
        },
        ...
      ]
    }
  }
}
```

* Get available slots of a particular doctors in next 7 days
```javascript
Get /api/schedule/?doctorId='789015934827950'
```

* Get available slots any doctors with a particular specialization
```javascript
Get /api/schedule/?specialization='neurosurgeon'
```

* Get available doctors within a particular timeslot
```javascript
Get /api/schedule/?fromDate='2020-09-01'&toDate='2020-09-02'
```

### Appointments
* CRUD API endpoints simillar to Patients
* get list of all appointments of a particluar doctor
```javascript
Get /api/appointments/?doctorId='789015934827950'
```

* get list of all appointments of a particluar patient
```javascript
Get /api/appointments/?patientId='789015934827950'
```

* To get appointments of specific status, send status in query parameters as such
```javascript
Get /api/appointments/?status='CONFIRMED'
```

* To get all appointments in a particular date
```javascript
Get /api/appointments/?appointmentDate='2020-09-02'
```

--- 
## Future Scope
* Provide more relevent search results based on patient's preferences
* Show doctor's initial and follow-up consulting fees
* Use statistics/ml on reviews and feedbacks to provide better experience