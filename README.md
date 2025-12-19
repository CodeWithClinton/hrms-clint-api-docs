---

# 👥 Staff Management API Documentation

This section documents all **Staff-related endpoints** exposed by the backend.
All endpoints require **authentication** using a valid token.

---

## 🔗 Base URL

```
https://api.example.com/api/
```

> All endpoint paths below are **relative to this base URL**.

---

## 🔐 Authentication

All endpoints require authentication.

**Header**

```http
Authorization: Bearer <your_access_token>
```

---

## 📦 Staff Serializer Response Format

All endpoints that return staff data use the format below:

```json
{
  "id": 1,
  "full_name": "John Doe",
  "email": "john@example.com",
  "phone_number": "08012345678",
  "date_of_birth": "1995-04-12",
  "gender": "Male",
  "department": "Engineering",
  "date_of_employment": "2023-01-10",
  "employment_type": "Full-time"
}
```

---

## 1️⃣ Add Staff

**Create a new staff profile and a linked user account**

* **Endpoint:** `POST /add_staff/`
* **Auth:** ✅ Required

### Request Body (JSON)

```json
{
  "first_name": "John",
  "last_name": "Doe",
  "other_name": "Michael",
  "full_name": "John Michael Doe",
  "email": "john@example.com",
  "phone_number": "08012345678",
  "date_of_birth": "1995-04-12",
  "gender": "Male",
  "dept_code": "ENG",
  "date_of_employment": "2023-01-10",
  "employment_type": "Full-time"
}
```

### Notes for Frontend

* `email` is **required**
* `full_name` is auto-generated if not provided
* A default password is auto-created for the staff user
* Department is created automatically if `dept_code` does not exist

### Success Response `201`

Returns the newly created staff object.

### Error Responses

* `400` – Validation or server error

---

## 2️⃣ Update Staff

**Update an existing staff profile**

* **Endpoint:** `POST /update_staff/{staff_id}/`
* **Auth:** ✅ Required

### URL Params

| Param    | Type    | Description               |
| -------- | ------- | ------------------------- |
| staff_id | integer | ID of the staff to update |

### Request Body (Partial Update Allowed)

```json
{
  "email": "newemail@example.com",
  "phone_number": "08099999999",
  "dept_code": "HR"
}
```

### Notes for Frontend

* You can send **only the fields you want to update**
* Updating email also updates the linked user’s username

### Success Response `200`

Returns the updated staff object.

### Error Responses

* `404` – Staff not found
* `400` – Validation or server error

---

## 3️⃣ Delete Staff

**Delete a staff profile and its linked user**

* **Endpoint:** `DELETE /delete_staff/{staff_id}/`
* **Auth:** ✅ Required

### URL Params

| Param    | Type    | Description     |
| -------- | ------- | --------------- |
| staff_id | integer | ID of the staff |

### Success Response `200`

```json
{
  "message": "Staff and linked user deleted successfully."
}
```

### Error Responses

* `404` – Staff not found
* `400` – Server error

---

## 4️⃣ List Staff

**Fetch all staff with optional filters**

* **Endpoint:** `GET /list_staff/`
* **Auth:** ✅ Required

### Query Parameters (Optional)

| Param           | Type   | Description               |
| --------------- | ------ | ------------------------- |
| dept_code       | string | Filter by department code |
| employment_type | string | Filter by employment type |
| search          | string | Search by name or email   |

### Example Request

```
GET /list_staff/?dept_code=ENG&search=john
```

### Success Response `200`

Returns an array of staff objects.

---

## 5️⃣ Get Staff by ID

**Fetch a single staff by ID**

* **Endpoint:** `GET /get_staff_by_id/{staff_id}/`
* **Auth:** ✅ Required

### URL Params

| Param    | Type    | Description |
| -------- | ------- | ----------- |
| staff_id | integer | Staff ID    |

### Success Response `200`

Returns a staff object.

### Error Responses

* `404` – Staff not found

---

## 6️⃣ Get Logged-In Staff Profile

**Fetch staff profile of the currently logged-in user**

* **Endpoint:** `GET /get_my_staff_info/`
* **Auth:** ✅ Required

### Success Response `200`

Returns the staff profile linked to the authenticated user.

### Error Responses

* `404` – Staff profile not found for user

---

## 7️⃣ Export Staff as CSV

**Download staff list as a CSV file**

* **Endpoint:** `GET /export_staff_csv/`
* **Auth:** ✅ Required

### Query Parameters (Optional)

Same as **List Staff**:

* `dept_code`
* `employment_type`
* `search`

### Response

* File download (`staff_export.csv`)
* `Content-Type: text/csv`

---

## 8️⃣ Upload Staff CSV

**Upload a CSV file to import staff (processed asynchronously)**

* **Endpoint:** `POST /upload_staff_csv/`
* **Auth:** ✅ Required
* **Content-Type:** `multipart/form-data`

### Form Data

| Field | Type | Description |
| ----- | ---- | ----------- |
| file  | file | CSV file    |

### Success Response `200`

```json
{
  "message": "CSV received. Processing in background.",
  "task_id": "c9f1a9d2-xxxx"
}
```

### Notes for Frontend

* Use the returned `task_id` to track import progress

---

## 9️⃣ Check Staff Import Status

**Check the status of a CSV import task**

* **Endpoint:** `GET /staff_import_status/{task_id}/`
* **Auth:** ✅ Required

### URL Params

| Param   | Type   | Description    |
| ------- | ------ | -------------- |
| task_id | string | Celery task ID |

### Success Response `200`

```json
{
  "task_id": "c9f1a9d2-xxxx",
  "status": "SUCCESS",
  "result": "Imported 120 staff records"
}
```

---


