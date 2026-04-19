# 🚀 Flutter Setup on macOS EC2 (Headless) — With Fixes

This document covers:

* Flutter installation
* Android SDK setup (without GUI)
* Java setup
* Common issues faced on EC2 Mac and how they were resolved

---

# 📦 1. Install Flutter

```bash
brew install flutter
```

Verify:

```bash
flutter --version
```

---

# ☕ 2. Install Java (Required for Android SDK)

```bash
brew install openjdk@17
```

Fix Java linking:

```bash
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

Set environment:

```bash
echo 'export JAVA_HOME=$(/usr/libexec/java_home -v 17)' >> ~/.zshrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
```

---

# 🤖 3. Install Android SDK (Headless - No Android Studio)

```bash
mkdir -p ~/Library/Android/sdk/cmdline-tools
cd ~/Library/Android/sdk/cmdline-tools

curl -O https://dl.google.com/android/repository/commandlinetools-mac-9477386_latest.zip
unzip commandlinetools-mac-9477386_latest.zip
mv cmdline-tools latest
```

---

# ⚙️ 4. Setup Environment Variables

```bash
cat <<EOF > ~/.zshrc
export ANDROID_HOME=\$HOME/Library/Android/sdk
export PATH=\$ANDROID_HOME/cmdline-tools/latest/bin:\$ANDROID_HOME/platform-tools:\$PATH
export JAVA_HOME=\$(/usr/libexec/java_home -v 17)
export PATH=\$JAVA_HOME/bin:\$PATH
EOF

source ~/.zshrc
```

---

# 📦 5. Install Required Android SDK Versions

```bash
sdkmanager --install "platform-tools" "platforms;android-36" "build-tools;36.0.0"
```

Accept licenses:

```bash
yes | sdkmanager --licenses
```

---

# 🌐 6. Install Chrome (Optional)

```bash
brew install --cask google-chrome
```

---

# 🍎 7. Setup Xcode

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
sudo xcodebuild -runFirstLaunch
```

---

# ✅ 8. Final Verification

```bash
flutter doctor
```

Expected:

```
[✓] Flutter
[✓] Android toolchain
[✓] Xcode
[✓] Chrome (optional)
```

---

# 🛠️ Issues Faced & Fixes

---

## ❌ Issue 1: Android Studio not opening

**Error:**

```
Launch failed... Domain does not support specified action
```

**Cause:**

* EC2 Mac is headless (no GUI support)

**Fix:**

* Avoid GUI tools like Android Studio
* Use Android SDK CLI (`sdkmanager`) instead

---

## ❌ Issue 2: Permission denied for `.zshrc`

**Error:**

```
zsh: permission denied: ~/.zshrc
```

**Fix:**

```bash
sudo chown ec2-user:staff ~/.zshrc
chmod u+w ~/.zshrc
```

---

## ❌ Issue 3: sshd error while sourcing `.zshrc`

**Error:**

```
sshd: no hostkeys available -- exiting
```

**Cause:**

* Invalid or unwanted commands in `.zshrc`

**Fix:**
Clean `.zshrc`:

```bash
cat <<EOF > ~/.zshrc
export ANDROID_HOME=\$HOME/Library/Android/sdk
export PATH=\$ANDROID_HOME/cmdline-tools/latest/bin:\$ANDROID_HOME/platform-tools:\$PATH
export JAVA_HOME=\$(/usr/libexec/java_home -v 17)
export PATH=\$JAVA_HOME/bin:\$PATH
EOF
```

---

## ❌ Issue 4: `sdkmanager` not found

**Cause:**

* PATH not set correctly

**Fix:**
Ensure:

```bash
echo $ANDROID_HOME
which sdkmanager
```

---

## ❌ Issue 5: Java not found

**Error:**

```
Unable to locate a Java Runtime
```

**Fix:**
Install Java:

```bash
brew install openjdk@17
```

---

## ❌ Issue 6: Android SDK version mismatch

**Error:**

```
Flutter requires Android SDK 36
```

**Fix:**

```bash
sdkmanager --install "platforms;android-36" "build-tools;36.0.0"
```

---

# 🎯 Final Outcome

* ✅ Flutter fully configured
* ✅ Android builds working
* ✅ iOS builds working (headless)
* ✅ CI/CD ready environment

---

# 🚀 Build Commands

```bash
flutter build apk
flutter build appbundle
flutter build ios
flutter build web
```

---

# 📌 Notes

* Android Emulator ❌ not supported on EC2
* Use CLI-based builds only
* Best suited for CI/CD pipelines

---

---

This version is:

* ✔ Real-world (based on your errors)
* ✔ Useful for team sharing
* ✔ Great for documentation / handover

---
# Prepared by:
*Shaik Moulali*
