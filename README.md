# 📘 Staff Management API Documentation

This section documents all **Staff-related endpoints** exposed by the backend.
All endpoints require **authentication**.

---

## 🔗 Base URL

All API endpoints are relative to the base URL below:

```text
https://api.hrms.appleadng.net/hrms/


## 🔐 Authentication

All endpoints require the user to be authenticated.

**Headers**

```http
Authorization: Bearer <access_token>
Content-Type: application/json
```

---

## 🧱 Staff Object (Response Shape)

Most endpoints return a `Staff` object with the following structure:

```json
{
  "id": 1,
  "full_name": "John Michael Doe",
  "email": "john.doe@example.com",
  "phone_number": "08012345678",
  "date_of_birth": "1995-06-15",
  "gender": "male",
  "department": "Computer Science",
  "date_of_employment": "2023-01-10",
  "employment_type": "full_time"
}
```

> **Note**

* `department` is returned as a **string (department name)**, not an object.
* Some internal fields (salary, is_active, user info) are intentionally not exposed.

---

## ➕ Add Staff

Create a new staff record and automatically create a linked user account.

**Endpoint**

```http
POST /add_staff/
```

### Request Body

```json
{
  "first_name": "John",
  "last_name": "Doe",
  "other_name": "Michael",
  "email": "john.doe@example.com",
  "phone_number": "08012345678",
  "date_of_birth": "1995-06-15",
  "gender": "male",
  "dept_code": "CSC",
  "date_of_employment": "2023-01-10",
  "employment_type": "full_time"
}
```

### Notes

* `email` is **required**
* `dept_code` will auto-create the department if it does not exist
* If `full_name` is not provided, it is auto-generated
* A default password is assigned to the staff user (to be changed later)

### Success Response (201)

Returns the newly created staff object.

---

## ✏️ Update Staff

Update an existing staff record.

**Endpoint**

```http
POST /update_staff/{staff_id}/
```

### Path Params

| Name     | Type   | Description               |
| -------- | ------ | ------------------------- |
| staff_id | number | ID of the staff to update |

### Request Body (Partial Updates Allowed)

```json
{
  "email": "new.email@example.com",
  "phone_number": "08099999999",
  "dept_code": "ENG",
  "employment_type": "contract"
}
```

### Notes

* You can send **only the fields you want to update**
* Updating `email` also updates the linked user's username
* `full_name` is auto-regenerated if name fields change

### Success Response (200)

Returns the updated staff object.

---

## 🗑️ Delete Staff

Delete a staff record **and the linked user account**.

**Endpoint**

```http
DELETE /delete_staff/{staff_id}/
```

### Success Response (200)

```json
{
  "message": "Staff and linked user deleted successfully."
}
```

### Error Responses

* `404` → Staff not found

---

## 📋 List Staff

Retrieve a list of staff with optional filters and search.

**Endpoint**

```http
GET /list_staff/
```

### Query Parameters (Optional)

| Param           | Description               | Example     |
| --------------- | ------------------------- | ----------- |
| dept_code       | Filter by department code | `CSC`       |
| employment_type | Filter by employment type | `full_time` |
| search          | Search by name or email   | `john`      |

### Example

```http
GET /list_staff/?dept_code=CSC&search=john
```

### Success Response (200)

```json
[
  {
    "id": 1,
    "full_name": "John Doe",
    "email": "john.doe@example.com",
    "department": "Computer Science",
    "employment_type": "full_time"
  }
]
```

---

## 📤 Export Staff as CSV

Download staff records as a CSV file.

**Endpoint**

```http
GET /export_staff_csv/
```

### Query Parameters

Same as `list_staff`:

* `dept_code`
* `employment_type`
* `search`

### Response

* Content-Type: `text/csv`
* Automatically downloads a file named:

  ```
  staff_export.csv
  ```

---

## 📥 Upload Staff CSV (Bulk Import)

Upload a CSV file to import staff records **asynchronously**.

**Endpoint**

```http
POST /upload_staff_csv/
```

### Request (Form Data)

| Field | Type     | Required |
| ----- | -------- | -------- |
| file  | CSV file | ✅        |

### Success Response (200)

```json
{
  "message": "CSV received. Processing in background.",
  "task_id": "b8c9f9b4-3e9d-4f3a-9e31-xxxx"
}
```

> CSV processing is handled in the background using **Celery**.

---

## ⏳ Staff Import Status

Check the status of a CSV import task.

**Endpoint**

```http
GET /staff_import_status/{task_id}/
```

### Success Response

```json
{
  "task_id": "b8c9f9b4-3e9d-4f3a-9e31-xxxx",
  "status": "SUCCESS",
  "result": {
    "imported": 120,
    "skipped": 3
  }
}
```

### Status Values

* `PENDING`
* `STARTED`
* `SUCCESS`
* `FAILURE`

---

## 👤 Get Staff by ID

Retrieve a single staff record by ID.

**Endpoint**

```http
GET /get_staff_by_id/{staff_id}/
```

### Success Response (200)

Returns a single staff object.

### Error Response

* `404` → Staff not found

---

## 🙋 Get Logged-in Staff Profile

Retrieve the staff profile linked to the currently authenticated user.

**Endpoint**

```http
GET /get_my_staff_info/
```

### Success Response (200)

Returns the logged-in user's staff object.

### Error Response

* `404` → No staff profile linked to this user

---

## ⚠️ Error Response Format

All error responses follow this format:

```json
{
  "error": "Error message here"
}
```

---

