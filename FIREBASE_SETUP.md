# Firebase Setup Instructions

This application now uses Firebase Authentication and Firestore for user management and data storage.

## Setup Steps

### 1. Create a Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click "Add Project" or select an existing project
3. Follow the setup wizard to create your project

### 2. Enable Authentication

1. In your Firebase project, go to **Authentication**
2. Click **Get Started**
3. Enable **Email/Password** authentication
4. Click **Save**

### 3. Create Firestore Database

1. Go to **Firestore Database**
2. Click **Create Database**
3. Choose **Start in test mode** (for development)
4. Select a location for your database
5. Click **Enable**

### 4. Configure Firebase Web App

1. Go to **Project Settings** (gear icon)
2. Scroll down to **Your apps** section
3. Click on the **Web** icon (`</>`)
4. Register your app with a nickname (e.g., "Car Price Predictor")
5. Copy the Firebase configuration object

### 5. Add Firebase Config to Your Application

1. Open `car price predictor/templates/index.html`
2. Find the Firebase configuration section (around line 293)
3. Replace the placeholder values with your actual Firebase credentials:

```javascript
const firebaseConfig = {
  apiKey: "YOUR_ACTUAL_API_KEY",
  authDomain: "YOUR_ACTUAL_AUTH_DOMAIN",
  projectId: "YOUR_ACTUAL_PROJECT_ID",
  storageBucket: "YOUR_ACTUAL_STORAGE_BUCKET",
  messagingSenderId: "YOUR_ACTUAL_MESSAGING_SENDER_ID",
  appId: "YOUR_ACTUAL_APP_ID"
};
```

### 6. Set Up Firestore Security Rules

In your Firebase Console, go to **Firestore Database** > **Rules** and update:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Users can only access their own data
    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
      
      // Users can manage their own saved cars
      match /savedCars/{carId} {
        allow read, write, delete: if request.auth != null && request.auth.uid == userId;
      }
    }
  }
}
```

Click **Publish** to save the rules.

## Features Now Using Firebase

- ✅ **User Authentication**: Login and Signup with email/password
- ✅ **User Profile Data**: Stored in Firestore `users` collection
- ✅ **Saved Cars**: Stored per user in `users/{userId}/savedCars` subcollection
- ✅ **Real-time Updates**: Authentication state automatically synced
- ✅ **Data Persistence**: All user data stored in cloud

## Firestore Data Structure

```
users/
  {userId}/
    username: string
    email: string
    createdAt: timestamp
    savedCars/
      {carId}/
        company: string
        model: string
        year: string
        fuelType: string
        kms: number
        price: string
        date: string
        createdAt: timestamp
```

## Security Notes

- Make sure to update Firestore security rules before deploying to production
- Never expose your Firebase Admin SDK credentials in client-side code
- Consider enabling additional authentication methods (Google, Facebook, etc.)
- Regularly review your security rules and Firebase console for any suspicious activity

