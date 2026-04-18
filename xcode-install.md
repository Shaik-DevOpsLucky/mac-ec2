# 📦 Install Xcode on AWS Mac EC2 via S3

This guide explains how to install **Xcode** on a private Mac EC2 instance using an S3 bucket as a transfer medium.

---

## 🧭 Architecture Flow

```
Apple Developer → Windows Machine → S3 Bucket → Mac EC2 (Private) → Xcode Installation
```

---

# 🚀 Step 1: Download Xcode from Apple

1. Open:
   [https://developer.apple.com/download/all/](https://developer.apple.com/download/all/)

2. Login with your Apple Developer account

3. Search and download:

   ```
   Xcode_26.4.1_Universal.xip
   ```

4. Save locally:

   ```bash
   C:\Users\<your-username>\Downloads\Xcode_26.4.1_Universal.xip
   ```

---

# ☁️ Step 2: Upload Xcode to S3

```bash
aws s3 cp "C:\Users\<your-username>\Downloads\Xcode_26.4.1_Universal.xip" \
s3://<your-bucket-name>/ \
--expected-size 3000000000
```

> ⚠️ Ensure your IAM user/role has `s3:PutObject` permission

---

# 🔐 Step 3: Connect to Mac EC2 using SSM

```bash
aws ssm start-session \
  --target <your-instance-id>
```

> ⚠️ Ensure instance role has `s3:GetObject` permission

---

# 📥 Step 4: Download Xcode from S3 to EC2

```bash
aws s3 cp s3://<your-bucket-name>/Xcode_26.4.1_Universal.xip .
```

---

# 📦 Step 5: Extract Xcode

```bash
xip -x Xcode_26.4.1_Universal.xip
```

**Notes:**

* Takes ~10–20 minutes
* High CPU usage is normal
* Message like below is expected:

  ```
  xip: signing certificate was "Software Update"
  ```

---

# 📁 Step 6: Move Xcode to Applications

```bash
sudo mv Xcode.app /Applications/
```

---

# ⚙️ Step 7: Set Xcode as default

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
```

---

# 📜 Step 8: Accept License

```bash
sudo xcodebuild -license accept
```

---

# 🚀 Step 9: Run First-Time Setup

```bash
sudo xcodebuild -runFirstLaunch
```

---

# ✅ Step 10: Verify Installation

### 1. Check version

```bash
xcodebuild -version
```

Expected:

```
Xcode 26.4.1
```

---

### 2. Check developer path

```bash
xcode-select -p
```

Expected:

```
/Applications/Xcode.app/Contents/Developer
```

---

### 3. Check SDKs

```bash
xcodebuild -showsdks
```

---

### 4. Quick validation

```bash
xcodebuild -version && xcode-select -p
```

---

# 🧹 Step 11: Cleanup (Optional)

```bash
rm Xcode_26.4.1_Universal.xip
```

---

# 🔚 Step 12: Exit SSM Session

```bash
exit
```

OR press:

```
Ctrl + D
```

---

# ⚠️ Prerequisites

### IAM Permissions

**EC2 Role:**

```json
s3:GetObject
```

**Windows IAM User:**

```json
s3:PutObject
```

---

### Disk Space

Ensure at least **20–30 GB free**:

```bash
df -h
```

---

# 🧩 Troubleshooting

### ❌ Access Denied (S3)

* Check IAM role or bucket policy

### ❌ xcodebuild not found

```bash
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
```

### ❌ License errors

```bash
sudo xcodebuild -license accept
```

### ❌ Extraction slow/stuck

* Wait — this is normal for `.xip` files

---

# ✅ Final Outcome

After completion, your Mac EC2 is ready for:

* iOS builds
* Flutter iOS builds
* CI/CD pipelines

---
# Prepared by:
*Shaik Moulali*
#DevOps Consultant
