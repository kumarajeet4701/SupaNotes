# SupaNotes

A simple and clean notes application built with React Native and Supabase. Create, edit, delete, and manage your personal notes with ease. All your data is securely stored and synced with Supabase.

## Features

- **Authentication** - Sign up and log in with email & password
- **Create Notes** - Add new notes with title and content
- **Edit Notes** - Update existing notes anytime
- **Delete Notes** - Remove notes you no longer need
- **Search Notes** - Find notes by title with real-time search
- **Auto-save** - Notes auto-save when you navigate back
- **Offline Support** - App works smoothly even without internet
- **Session Persistence** - Stay logged in even after closing the app
- **Pull-to-Refresh** - Refresh your notes list with a simple pull gesture
- **User Privacy** - See only your own notes (secure with RLS policies)

## 🛠 Tech Stack

- **React Native** - Mobile app framework
- **Supabase** - Backend (Authentication + Database)
- **Formik** - Form validation
- **Yup** - Schema validation
- **React Navigation** - Navigation between screens
- **AsyncStorage** - Local session storage

## 📋 Project Structure

```
src/
├── api/              # Supabase client setup
├── assets/           # Images and icons
├── config/           # Environment variables (Supabase credentials)
├── constants/        # Colors, styles, images
├── context/          # Auth context (state management)
├── hooks/            # Custom hooks (useNotes, useDebounce)
├── navigation/       # Navigation setup
├── utils/            # Helper functions, validation schemas
└── views/            # Screen components
    ├── components/   # Reusable UI components
    └── screens/      # Auth and Notes screens
```

### Running the App
npx react-native run-android

## 📱 Building APK

**Build APK (for testing):**
```bash
cd android
./gradlew assembleDebug
cd ..
```

Output: `android/app/build/outputs/apk/debug/app-debug.apk`

## 🗄 Supabase Setup

### 1. Create the Notes Table

Run this SQL in your Supabase project's SQL Editor:

```sql
create table notes (
  id uuid primary key default gen_random_uuid(),
  user_id uuid not null references auth.users(id) on delete cascade,
  title text not null,
  content text not null,
  created_at timestamptz not null default now(),
  updated_at timestamptz not null default now()
);

alter table notes enable row level security;

create policy "Select own notes" on notes for select using (auth.uid() = user_id);
create policy "Insert own notes" on notes for insert with check (auth.uid() = user_id);
create policy "Update own notes" on notes for update using (auth.uid() = user_id);
create policy "Delete own notes" on notes for delete using (auth.uid() = user_id);
```

### . Disable Email Confirmation
Go to **Authentication → Providers → Email**


This lets users sign up without email verification.

## How Authentication Works

- Users sign up with **email + password + full name**
- Full name is saved to user metadata
- Session stored locally using **AsyncStorage**
- Session auto-restores on app startup
- Passwords encrypted by Supabase
- Users can only see their own notes (enforced by RLS)

## How Notes Work

- Each note has: **id, user_id, title, content, created_at, updated_at**
- Users see only their own notes (RLS policy enforces this)
- Notes auto-save when navigating back
- Search filters notes by title in real-time (debounced 300ms)
- Delete removes note instantly

## Offline Mode

The app works offline but with limits:

- ✅ Can view saved notes
- ✅ Can logout
- ❌ Cannot create/edit notes (requires internet)
- ❌ Cannot refresh (requires internet)
- Shows "You are offline" message

## Screens Overview

| Screen | Purpose |
|--------|---------|
| **Login** | Sign in with email + password |
| **Signup** | Create new account with full name |
| **Notes List** | View all notes, search, pull-to-refresh |
| **Note Detail** | Create or edit a note |

## Auto-Save Feature

1. User types title and content
2. User presses back button
3. If both fields filled → note saves automatically
4. If not filled → shows error, doesn't save
5. User returned to notes list

## Key Features Explained

**Pull-to-Refresh:** Swipe down on notes list to refresh from server

**Debounced Search:** Search waits 300ms after you stop typing before filtering

**Sidebar:** Shows user's full name and email, has logout button

**Session Persistence:** Closes app → opens app → still logged in

