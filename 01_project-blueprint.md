# PROJECT BLUEPRINT: Cross-Platform Native Android & Web App

You are an expert full-stack mobile and web developer AI agent. Your task is to build, test, and maintain a cross-platform application from a single repository. We will prioritize native Android performance and seamless localhost web browser testing, with zero architecture choices that would block an iOS release in the future.

## 1. THE TECH STACK (Mandatory Requirements)
* **Core Framework:** React Native with Expo (Managed Workflow) using TypeScript. Do not use bare React Native or custom native iOS/Android code.
* **Routing & Navigation:** `expo-router` (File-based routing). All screens must live in the `/app` directory to ensure routing works identically across web URLs and native mobile view stacks.
* **Backend, Auth & Database:** Supabase (`@supabase/supabase-js`). 
  * Use `@react-native-async-storage/async-storage` as the custom storage adapter for Supabase Auth session persistence so user logins persist natively on mobile and in web local storage.
* **Styling:** NativeWind (Tailwind CSS for React Native) or standard React Native `StyleSheet`. Avoid web-only CSS libraries (like Bootstrap, traditional Tailwind, or styled-components) that crash native mobile view engines.
* **Icons:** `@expo/vector-icons` (Lucide or Ionicons preferred).

## 2. CORE CODING GUARDRAILS FOR THE AI AGENT
1. **No Web-Only HTML Tags:** Never write raw `<div>`, `<span>`, `<img>`, or `<p>` tags in component files. Always use universal React Native primitives (`<View>`, `<Text>`, `<Image>`, `<TouchableOpacity>`, `<ScrollView>`) so Expo can compile them cleanly to native Android widgets and web DOM elements simultaneously.
2. **Universal SDKs Only:** Whenever adding features (camera, haptics, secure vault, push notifications), you MUST use official `@expo/...` SDK packages. Never install libraries that require manual `npx pod-install` or custom Android Manifest edits unless explicitly instructed.
3. **Responsive Layouts:** Ensure layouts scale gracefully between desktop web widths and vertical mobile screens by utilizing Flexbox and percentage-based/responsive dimensions.

## 3. THE 3-PHASE DEVELOPMENT WORKFLOW
We will follow a strict, tool-light workflow without installing Android Studio or heavy local SDKs:

* **Phase 1: Rapid UI & Logic Validation (Local Web)**
  * Execute `npx expo start --web` to launch the development server.
  * We will validate layouts, test forms, and check Supabase database queries directly in the local desktop browser at `http://localhost:8081`.
* **Phase 2: Native Android Hardware & Feel Testing (Expo Go)**
  * Execute `npx expo start` to generate a terminal QR code.
  * We will scan this QR code using the physical **Expo Go** Android app on a real smartphone over Wi-Fi to test animations, touch gestures, haptics, and camera performance.
* **Phase 3: Production Android APK/AAB Compilation (EAS Cloud)**
  * When ready for standalone testing or Google Play Store deployment, execute `eas build -p android --profile preview` (for direct `.apk` installation on devices) or `--profile production` (for `.aab` store bundles).
  * Do not attempt local native Android Gradle builds; rely entirely on Expo Application Services (EAS) cloud compilation.