# Multi-Community Admission & Enrollment System

## Database Schema (MongoDB)

### 1. Users Collection (`users`)
Stores information about all users, including Super Admins, School Admins, and Parents.

```json
{
  "_id": ObjectId,
  "fullName": String,
  "email": String,
  "password": String (hashed),
  "role": String ("super_admin" | "school_admin" | "parent"),
  "contactNumber": String,
  "address": {
    "street": String,
    "city": String,
    "state": String,
    "zipCode": String
  },
  "schoolId": ObjectId (nullable, only for school_admin or parent),
  "createdAt": Date,
  "updatedAt": Date
}
```

### 2. Super Admin Collection (`super_admins`)
Stores Super Admins who can sell and manage multiple schools.

```json
{
  "_id": ObjectId,
  "userId": ObjectId,  // Reference to `users` collection
  "totalSchoolsManaged": Number,
  "revenueGenerated": Number,
  "subscriptionPlans": [
    {
      "planName": String,
      "price": Number,
      "billingCycle": String ("monthly" | "quarterly" | "yearly")
    }
  ],
  "createdAt": Date,
  "updatedAt": Date
}
```

### 3. Schools Collection (`schools`)
Stores details of schools using the system, including their branding (logo, theme, CMS settings).

```json
{
  "_id": ObjectId,
  "name": String,
  "email": String,
  "contactNumber": String,
  "address": {
    "street": String,
    "city": String,
    "state": String,
    "zipCode": String
  },
  "subscriptionPlan": ObjectId,  // Reference to `super_admins.subscriptionPlans`
  "superAdminId": ObjectId,  // Reference to `super_admins`
  "cmsSettings": {
    "logoUrl": String,
    "theme": {
      "primaryColor": String,
      "secondaryColor": String,
      "fontStyle": String
    },
    "homepageBanner": String,
    "customFooterText": String
  },
  "createdAt": Date,
  "updatedAt": Date
}
```

### 4. Parents Collection (`parents`)
Stores parents and their associated students in an embedded structure.

```json
{
  "_id": ObjectId,
  "userId": ObjectId,  // Reference to `users` collection
  "schoolId": ObjectId,  // The school the parent is registered under
  "students": [
    {
      "studentId": ObjectId,
      "fullName": String,
      "dateOfBirth": Date,
      "gradeLevel": String,
      "gender": String,
      "allergiesMedicalConditions": String,
      "specialNeeds": Boolean,
      "status": String ("pending" | "approved" | "rejected")
    }
  ],
  "createdAt": Date,
  "updatedAt": Date
}
```

### 5. Courses Collection (`courses`)
Stores course offerings per school.

```json
{
  "_id": ObjectId,
  "schoolId": ObjectId,  // Reference to `schools`
  "name": String,
  "description": String,
  "duration": Number (in months),
  "fee": Number,
  "createdAt": Date,
  "updatedAt": Date
}
```

### 6. Enrollments Collection (`enrollments`)
Tracks student enrollments in courses.

```json
{
  "_id": ObjectId,
  "studentId": ObjectId,  // Reference to `parents.students.studentId`
  "courseId": ObjectId,  // Reference to `courses`
  "schoolId": ObjectId,  // Reference to `schools`
  "status": String ("active" | "cancelled" | "completed"),
  "startDate": Date,
  "endDate": Date,
  "createdAt": Date,
  "updatedAt": Date
}
```

### 7. Payments Collection (`payments`)
Stores payment details for courses.

```json
{
  "_id": ObjectId,
  "transactionId": String,
  "parentId": ObjectId,  // Reference to `parents`
  "studentId": ObjectId,  // Reference to `parents.students.studentId`
  "courseId": ObjectId,  // Reference to `courses`
  "amount": Number,
  "paymentMethod": String ("credit_card" | "debit_card" | "paypal" | "ach_transfer"),
  "paymentStatus": String ("pending" | "completed" | "failed"),
  "date": Date,
  "createdAt": Date,
  "updatedAt": Date
}
```

### 8. Subscriptions Collection (`subscriptions`)
Tracks school subscription plans under the Super Admin.

```json
{
  "_id": ObjectId,
  "schoolId": ObjectId,  // Reference to `schools`
  "planName": String,
  "amount": Number,
  "billingCycle": String ("monthly" | "quarterly" | "yearly"),
  "status": String ("active" | "expired" | "cancelled"),
  "nextBillingDate": Date,
  "createdAt": Date,
  "updatedAt": Date
}
```

### 9. CMS Customization Collection (`cms_settings`)
Allows each school to manage its own branding, theme, and content.

```json
{
  "_id": ObjectId,
  "schoolId": ObjectId,  // Reference to `schools`
  "logoUrl": String,
  "theme": {
    "primaryColor": String,
    "secondaryColor": String,
    "fontStyle": String
  },
  "homepageBanner": String,
  "customFooterText": String,
  "customPages": [
    {
      "title": String,
      "content": String,
      "slug": String
    }
  ],
  "createdAt": Date,
  "updatedAt": Date
}
```

## Key Features in This Database Schema
✅ Super Admin can sell, resell, and manage multiple schools.
✅ Parents have embedded student details inside their collection.
✅ Schools can customize their logo, theme, and website content (CMS settings).
✅ Automated recurring payments via Stripe/PayPal.
✅ Role-based access control for Super Admins, School Admins, and Parents.