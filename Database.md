# Multi-Community Admission & Enrollment System - Database Schema

## Overview
This document outlines the advanced database schema for the Multi-Community Admission & Enrollment System. The system supports multiple communities, with a Super Admin managing communities, Community Directors overseeing individual communities, and Schools (Parents) enrolling students.

## Database Collections

### 1. Users Collection (`users`)
Stores details of all users including Super Admins, Community Directors, and Schools (Parents).

```json
{
  "_id": ObjectId,
  "fullName": String,
  "email": String,
  "password": String (hashed),
  "role": String ("super_admin" | "community_director" | "school"),
  "contactNumber": String,
  "address": {
    "street": String,
    "city": String,
    "state": String,
    "zipCode": String
  },
  "communityId": ObjectId (nullable, assigned to Community Directors and Schools),
  "createdAt": Date,
  "updatedAt": Date
}
```

### 2. Super Admin Collection (`super_admins`)
Stores information about Super Admins who manage and sell communities.

```json
{
  "_id": ObjectId,
  "userId": ObjectId,  // Reference to `users` collection
  "totalCommunitiesManaged": Number,
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

### 3. Communities Collection (`communities`)
Stores community details and CMS settings.

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
  "superAdminId": ObjectId,  // Reference to `super_admins`
  "communityDirectorId": ObjectId,  // Reference to `users` (role: community_director)
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

### 4. Schools Collection (`schools`)
Stores details of Schools (Parents) registered under communities.

```json
{
  "_id": ObjectId,
  "userId": ObjectId,  // Reference to `users` collection
  "communityId": ObjectId,  // Reference to `communities`
  "students": [ObjectId],  // References `students` collection
  "createdAt": Date,
  "updatedAt": Date
}
```

### 5. Students Collection (`students`)
Stores student details associated with Schools.

```json
{
  "_id": ObjectId,
  "schoolId": ObjectId,  // Reference to `schools`
  "fullName": String,
  "dateOfBirth": Date,
  "gradeLevel": String,
  "gender": String,
  "allergiesMedicalConditions": String,
  "specialNeeds": Boolean,
  "status": String ("pending" | "approved" | "rejected"),
  "createdAt": Date,
  "updatedAt": Date
}
```

### 6. Products Collection (`products`)
Stores courses, materials, and services available for purchase.

```json
{
  "_id": ObjectId,
  "communityId": ObjectId,  // Reference to `communities`
  "name": String,
  "description": String,
  "price": Number,
  "prerequisites": [ObjectId],  // References `products`
  "visibility": String ("global" | "community-specific"),
  "createdAt": Date,
  "updatedAt": Date
}
```

### 7. Enrollments Collection (`enrollments`)
Tracks student enrollments in courses/products.

```json
{
  "_id": ObjectId,
  "studentId": ObjectId,  // Reference to `students`
  "productId": ObjectId,  // Reference to `products`
  "communityId": ObjectId,  // Reference to `communities`
  "status": String ("active" | "cancelled" | "completed"),
  "startDate": Date,
  "endDate": Date,
  "createdAt": Date,
  "updatedAt": Date
}
```

### 8. Payments Collection (`payments`)
Stores payment transactions.

```json
{
  "_id": ObjectId,
  "transactionId": String,
  "schoolId": ObjectId,  // Reference to `schools`
  "studentId": ObjectId,  // Reference to `students`
  "productId": ObjectId,  // Reference to `products`
  "amount": Number,
  "paymentMethod": String ("credit_card" | "debit_card" | "plaid" | "helcim"),
  "paymentStatus": String ("pending" | "completed" | "failed"),
  "date": Date,
  "createdAt": Date,
  "updatedAt": Date
}
```

### 9. CMS Settings Collection (`cms_settings`)
Stores community-specific CMS customization details.

```json
{
  "_id": ObjectId,
  "communityId": ObjectId,  // Reference to `communities`
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

## Relationships
- `users.role` determines access control.
- `super_admins` manage `communities` and oversee `community_directors`.
- `community_directors` manage `schools`, `products`, and `enrollments`.
- `schools` (parents) register students and purchase products.
- `students` are linked to `schools` and can be enrolled in `products`.
- `payments` track transactions for `products`.
- `cms_settings` allow each community to modify its website content.

## Features Covered ✅
✔️ Super Admin can sell, resell, and manage multiple communities.
✔️ Community Directors manage CMS branding, courses, and enrollments.
✔️ Schools (Parents) can apply for membership in multiple communities.
✔️ Products include courses, materials, and services.
✔️ Payments are handled through Helcim and Plaid (one-time payments only).
✔️ CMS allows communities to customize themes, logos, and pages.
✔️ Secure, role-based authentication (JWT, Google, Email Login Link).


