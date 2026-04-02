# 🩸 Gluconova — Firebase Edition

**No Flask. No server. Pure Firebase — Auth + Realtime Database.**
Works as a Web App and installable PWA on Android.

---

## 📁 Project Structure

```
gluconova-firebase/
├── index.html              ← Login / Register (Firebase Auth + Google Sign-In)
├── dashboard.html          ← Full dashboard (RTDB real-time sync)
├── manifest.json           ← PWA manifest (install on Android)
├── firebase.json           ← Firebase Hosting config
├── database.rules.json     ← RTDB security rules
└── icons/
    ├── icon-192.png        ← Add your app icon
    └── icon-512.png        ← Add your app icon
```

---

## ⚡ Deploy to Firebase Hosting (5 minutes)

```bash
# 1. Install Firebase CLI
npm install -g firebase-tools

# 2. Login
firebase login

# 3. Go to project folder
cd gluconova-firebase

# 4. Initialize (select your project gluconova-e0c81)
firebase init hosting

# 5. Deploy database rules
firebase deploy --only database

# 6. Deploy the web app
firebase deploy --only hosting
```

Your app will be live at:
**https://gluconova-e0c81.web.app**

---

## 🔥 Enable Firebase Services

Go to [Firebase Console](https://console.firebase.google.com/project/gluconova-e0c81):

### 1. Authentication
- Build → Authentication → Sign-in method
- Enable: **Email/Password** ✓
- Enable: **Google** ✓ (add your SHA-1 for Android)

### 2. Realtime Database
- Build → Realtime Database → Create database
- Start in **test mode**, then deploy `database.rules.json`

### 3. Hosting (already set up via firebase.json)

---

## 📱 Install on Android as PWA

1. Open **https://gluconova-e0c81.web.app** in Chrome
2. Tap the **⋮ menu** → "Add to Home Screen"
3. The app installs like a native app — no Play Store needed!

---

## 🔌 ESP32 Integration

Send sensor readings directly to Firebase from ESP32:

```cpp
// ESP32 Arduino — send glucose to Firebase RTDB
#include <WiFi.h>
#include <HTTPClient.h>
#include <ArduinoJson.h>

const char* FIREBASE_URL = "https://gluconova-e0c81-default-rtdb.firebaseio.com";
const char* USER_UID = "YOUR_USER_UID";  // Get from Firebase Auth console
const char* DB_SECRET = "YOUR_DB_SECRET"; // Or use Firebase Auth token

void sendGlucoseReading(float value) {
  HTTPClient http;
  String url = String(FIREBASE_URL) + "/users/" + USER_UID + "/glucose.json?auth=" + DB_SECRET;
  
  DynamicJsonDocument doc(128);
  doc["value"] = value;
  doc["source"] = "esp32";
  doc["timestamp"] = millis(); // Use NTP time in production
  
  String body;
  serializeJson(doc, body);
  
  http.begin(url);
  http.addHeader("Content-Type", "application/json");
  int code = http.POST(body);
  http.end();
}
```

---

## 🗄️ Firebase RTDB Data Structure

```
users/
  {uid}/
    profile/
      name: "Srikanth"
      email: "user@example.com"
      createdAt: 1712000000000
      targetMin: 70
      targetMax: 140
    glucose/
      {pushId}/
        value: 98.5
        source: "sensor" | "manual" | "simulated"
        timestamp: 1712000000000
    foodLogs/
      {pushId}/
        food: "white rice"
        portion: 1.5
        predictedSpike: 32.4
        gi: 72
        timestamp: 1712000000000
```

---

## ✅ Features

| Feature | Status |
|---------|--------|
| Firebase Email Auth | ✅ |
| Google Sign-In | ✅ |
| Real-time glucose sync | ✅ |
| 7-day trend chart | ✅ |
| Live glucose ring meter | ✅ |
| AI food predictor (70+ foods) | ✅ |
| Meal logging → Firebase | ✅ |
| Weekly report + bar chart | ✅ |
| Smart alerts (low/high/critical) | ✅ |
| ESP32 sensor simulation | ✅ |
| RTDB security rules | ✅ |
| PWA — installable on Android | ✅ |
| Firebase Hosting ready | ✅ |
| Responsive (mobile-first) | ✅ |

---

## 🔐 Security Checklist

- [ ] Restrict API key in Google Cloud Console to your domain
- [ ] Deploy `database.rules.json` before going live
- [ ] Never expose `google-services.json` in a public GitHub repo
- [ ] Add `.gitignore` entry for `google-services.json`

```.gitignore
google-services.json
google-services-info.plist
.env
```
