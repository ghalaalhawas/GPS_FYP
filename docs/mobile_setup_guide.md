# React Native Mobile App Setup Guide
**Week 4-5: January 20 - February 2, 2026**

---

## Decision: React Native with Expo

Based on our technology stack research, we've chosen **React Native with Expo** for:
- Cross-platform development (iOS + Android)
- Faster development cycle
- Excellent GPS and mapping support
- Large community and resources

---

## Prerequisites

### 1. Node.js and npm

**Windows:**
1. Download from: https://nodejs.org/ (LTS version)
2. Run installer
3. Verify:
```powershell
node --version  # Should be v18 or higher
npm --version   # Should be v9 or higher
```

**Mac:**
```bash
# Using Homebrew
brew install node

# Verify
node --version
npm --version
```

### 2. Git (Already installed from Week 2)

### 3. VS Code (Recommended IDE)

Extensions to install:
- React Native Tools
- ES7+ React/Redux/React-Native snippets
- Prettier - Code formatter

---

## Setup Steps

### Step 1: Install Expo CLI

```powershell
# Install globally
npm install -g expo-cli

# Verify installation
expo --version
```

### Step 2: Create Project Directory

```powershell
# Navigate to project root
cd C:\Users\bmezher\Desktop\FYP\Ghala

# Create mobile app directory
mkdir mobile_app
cd mobile_app
```

### Step 3: Initialize React Native with Expo

```powershell
# Create new Expo project
npx create-expo-app@latest GhalaSafetyApp

# Navigate into project
cd GhalaSafetyApp

# Start development server
npx expo start
```

### Step 4: Install Expo Go App on Your Phone

**Android:** https://play.google.com/store/apps/details?id=host.exp.exponent  
**iOS:** https://apps.apple.com/app/expo-go/id982107779

### Step 5: Test on Device

1. Run `npx expo start`
2. Scan QR code with Expo Go app
3. App should load on your device

---

## Installing Required Dependencies

### Core Libraries

```powershell
# Navigate to mobile app directory
cd C:\Users\bmezher\Desktop\FYP\Ghala\mobile_app\GhalaSafetyApp

# Install mapping library
npx expo install react-native-maps

# Install location services
npx expo install expo-location

# Install storage
npx expo install @react-native-async-storage/async-storage

# Install navigation (if needed later)
npm install @react-navigation/native
npm install @react-navigation/stack
npx expo install react-native-screens react-native-safe-area-context

# Install spatial indexing library
npm install rbush
```

---

## Project Structure

```
mobile_app/GhalaSafetyApp/
├── App.js                 # Main app entry point
├── package.json           # Dependencies
├── app.json              # Expo configuration
├── src/
│   ├── components/       # Reusable components
│   │   ├── Map.js
│   │   ├── WarningOverlay.js
│   │   └── HazardMarker.js
│   ├── services/         # Business logic
│   │   ├── LocationService.js
│   │   ├── HazardManager.js
│   │   └── WarningManager.js
│   ├── utils/            # Utility functions
│   │   ├── distance.js
│   │   └── bearing.js
│   ├── screens/          # App screens
│   │   ├── MapScreen.js
│   │   └── SettingsScreen.js
│   └── data/             # Sample data
│       └── sample_hazards.json
├── assets/               # Images, fonts, etc.
└── .gitignore
```

---

## Common Issues and Solutions

### Issue 1: "Expo CLI not found"
**Solution:**
```powershell
npm install -g expo-cli --force
```

### Issue 2: Can't connect to development server
**Solution:**
- Ensure phone and computer on same Wi-Fi network
- Disable VPN if active
- Try tunnel mode: `npx expo start --tunnel`

### Issue 3: Maps not loading
**Solution:**
- Check `app.json` has proper permissions
- Add to `app.json`:
```json
{
  "expo": {
    "ios": {
      "infoPlist": {
        "NSLocationWhenInUseUsageDescription": "This app needs access to location for navigation."
      }
    },
    "android": {
      "permissions": ["ACCESS_FINE_LOCATION"]
    }
  }
}
```

---

## Testing Setup

### On Device (Recommended)
1. Install Expo Go app
2. Scan QR code from terminal
3. Test GPS and location services
4. Test on real hardware

### On Emulator (Optional)

**Android Emulator:**
1. Install Android Studio
2. Create AVD (Android Virtual Device)
3. Run: `npx expo start --android`

**iOS Simulator (Mac only):**
1. Install Xcode
2. Run: `npx expo start --ios`

---

## Development Workflow

### Daily Development:

```powershell
# Start development server
cd C:\Users\bmezher\Desktop\FYP\Ghala\mobile_app\GhalaSafetyApp
npx expo start

# The terminal will show:
# - QR code to scan
# - Options: press 'a' for Android, 'i' for iOS
# - Metro bundler logs
```

### Hot Reload:
- Save files to see changes instantly
- Shake device to open developer menu
- Reload app: 'r' in terminal

---

## Next Steps

After setup complete:

### Week 5 Tasks:
1. ✅ Environment set up
2. Create "Hello World" with map view
3. Implement GPS location tracking
4. Load sample hazard data
5. Test on device

### Week 8 Tasks:
6. Full map integration
7. User location display
8. Basic warning system

---

## Resources

- **Expo Docs:** https://docs.expo.dev/
- **React Native Maps:** https://github.com/react-native-maps/react-native-maps
- **Expo Location:** https://docs.expo.dev/versions/latest/sdk/location/
- **React Native Docs:** https://reactnative.dev/

---

**Status:** Ready for Week 5 Implementation  
**Last Updated:** January 21, 2026
