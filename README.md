# Peruri-ShaQR

![Version](https://img.shields.io/badge/version-v1.0.0-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Python](https://img.shields.io/badge/python-3.12%2B-blue)
---

Peruri-ShaQR is a Python package for generating secure QR codes that visually embed hidden data within standard QR content. The resulting QR remains compatible with general QR readers, while also supporting the extraction of embedded secret data using custom decoders.

# Features
- Embed hidden messages within a visible QR text.
- Optional encryption of the hidden message using a custom key.
- Automatically selects the optimal QR masking pattern for better quality.
- Outputs the result as a PNG image, encoded in Base64.

# Usage
### Import and Create QR Code
```python
from shaqr import ShaQR

# Basic usage without key
qr = ShaQR.create_shaqr("Visible Text", "Secret")

# With encryption key
qr = ShaQR.create_shaqr("Visible Text", "Secret", "key123")
```
### Retrieve QR Code as Base64 PNG
```python
# Get base64-encoded PNG
base64_png = qr.get_coderesult()

# Optionally save to file
import base64
with open("output.png", "wb") as f:
    f.write(base64.b64decode(base64_png))
```
### Get QR Version
```python
version = qr.get_version()
print("QR Code Version:", version)
```
# Constraints
- Maximum length for visible text: 40 characters
- Maximum length for hidden text: 12 characters
- Maximum key length (if used): 9 characters

If the input exceeds these limits, a **`ValueError`** will be raised.

> **Note:**  
> Avoid exceeding the specified limits, as it may degrade QR code readability due to scale adjustments.


# Example
```python
from shaqr import ShaQR

# Create ShaQR instance with encryption
qr = ShaQR.create_shaqr("Welcome", "topsecret", "mykey")

# Decode and save
with open("welcome_qr.png", "wb") as f:
    f.write(base64.b64decode(qr.get_coderesult()))
```
------------------------------------------------


# Peruri-ShaQR-Scanner-SDK

![version](https://img.shields.io/badge/version-v1.0.0-informational)
![license](https://img.shields.io/badge/license-MIT-brightgreen)
![kotlin](https://img.shields.io/badge/kotlin-1.9+-7F52FF?logo=kotlin&logoColor=white)
---

Peruri ShaQR SDK is a secure and customizable Android library for scanning and decoding enhanced Peruri ShaQR codes with encryption

## Requirements

To integrate this SDK into your Android project, ensure your project meets the following requirements:
- Minimum Android SDK: 24
- Compile SDK Version: 35
- Java Compatibility: Java 11 (sourceCompatibility and targetCompatibility)
- Kotlin Compatibility: JVM target 11

# Features
- Scan ShaQR codes and standard QR codes using Camera.
- Support scanning ShaQR codes from Gallery images.

# Instalation
Add .aar to your project
1. Place the peruri-shaqr-sdk.aar file in your app’s libs/ folder.
2. Add the following to your app/build.gradle:
```kotlin
dependencies {
    implementation("androidx.camera:camera-core:1.5.0-alpha03")
    implementation("androidx.camera:camera-camera2:1.5.0-alpha03")
    implementation("androidx.camera:camera-lifecycle:1.5.0-alpha03")
    implementation("androidx.camera:camera-view:1.5.0-alpha03")
    implementation("com.google.mlkit:object-detection:17.0.2")
    implementation("com.google.mlkit:barcode-scanning:17.3.0")

    implementation(files("libs/peruri-shaqr-sdk-1.0.0.aar"))
    implementation(fileTree(mapOf("dir" to "libs", "include" to listOf("*.jar", "*.aar"))))
}
```
# Usage
### Launch Scanner

- Register Scanner Launcher
```kotlin
private val scannerLauncher = registerForActivityResult(
    ActivityResultContracts.StartActivityForResult()
) { result ->
    if (result.resultCode == Activity.RESULT_OK) {
        val scanned = result.data?.getStringExtra(ShaQR.SCAN_RESULT)

        // handle scanned result here
    }
}
```

- Launch the Scanner
```kotlin
val shaqrIntent = ShaQR.getIntent(this)
scannerLauncher.launch(shaqrIntent)
```

### Decode the Result
```kotlin
val decoder = ShaQRDecoder.Builder().config {
    setEncryptionKey("peruri123") // Optional
}.build()

val hidden = decoder.decode(scanned)
val visible = decoder.extractVisibleText(scanned)
```
