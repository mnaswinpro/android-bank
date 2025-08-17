# Security

## How can we make our applications more secure?

### exported attribute
Set android:exported="false" in your AndroidManifest.xml to prevent other apps from starting your activity.

### Intents and Permissions: 
If your activity needs to be accessible by other apps, use intent filters with appropriate permissions to control 
which apps can interact with it.

android:protectionLevel:
normal: Granted automatically if requested.
dangerous: Requires user approval at runtime.
signature: Granted only if the requesting app is signed with the same certificate as your app.
signatureOrSystem: Like signature, but also granted to system apps.

```
// Define a Custom Permission
<manifest ...>
    <permission
        android:name="com.myapp.permission.ACCESS_MY_ACTIVITY"
        android:protectionLevel="signature" />
    <application ...>
        </application>
</manifest>

// Enforce the Permission on Your Activity
<activity
    android:name=".MySecureActivity"
    android:exported="true"
    android:permission="com.myapp.permission.ACCESS_MY_ACTIVITY" > 
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```

### Restrict Content Providers
If your activity interacts with content providers, ensure they have proper read and write permissions to prevent unauthorized access.

### Input Validation
Always validate user input to prevent malicious data from compromising your app. Use appropriate input types 
and sanitize data to avoid issues like SQL injection or cross-site scripting (XSS).

### Flags to Secure Window Content
Use window flags like FLAG_SECURE to prevent screenshots and screen recording of your activity.
```
window.setFlags(WindowManager.LayoutParams.FLAG_SECURE, 
                WindowManager.LayoutParams.FLAG_SECURE)
```

### ProGuard
Use ProGuard to obfuscate your code and make it more difficult for attackers to reverse engineer your app.

### Regular Security Updates
Keep your app's dependencies and Android SDK up-to-date to benefit from the latest security patches.

### Root Detection
Implement root detection mechanisms to identify potentially compromised devices and take appropriate action (e.g., restrict 
functionality or warn the user).

## How to store API keys within your application securely?

### Don't Store It At All (Ideal)
Method: If feasible, avoid storing the API key directly in your app. Use a backend server to handle API requests 
and authentication. Your app communicates with your server, which then interacts with the external API using the key.
Pros: Highest security, easy key rotation/revocation,  minimizes risk if your app is compromised.
Cons: Requires setting up and maintaining a backend server, might not be suitable for all app architectures.

### Android Keystore System (Recommended)
Method: Utilize the Android Keystore system to generate keys and encrypt your API key. This provides hardware-backed security, making it very difficult for attackers to extract the key.
Pros: Strong security, relatively easy to implement, integrates well with Android.
Cons: Requires understanding of Keystore concepts, might have limitations on older Android versions.

### NDK and Obfuscation
Method: Store the API key in native C/C++ code using the NDK. Obfuscate the code to make reverse engineering more challenging.
Pros: Adds an extra layer of security, makes it harder to extract the key directly from the APK.
Cons: More complex to implement, requires NDK knowledge, obfuscation can be bypassed with dedicated effort.

## What are the other ways to store API keys which are insecure?

### BuildConfig (Not Recommended)
Method: Define the API key as a string resource in your build.gradle file. It will be accessible via BuildConfig.API_KEY.
Pros: Simple to implement, keeps the key out of version control if you exclude the generated BuildConfig file.
Cons: Very insecure. The key is easily extracted from the APK. Avoid this method for sensitive keys.

### Environment Variables (Situational)
Method: Set the API key as an environment variable during your build process. Access it in your code using System.getenv("API_KEY").
Pros: Can be useful for local development or CI/CD pipelines.
Cons: Not suitable for production as environment variables can be easily accessed on a rooted device.

### Hardcoding
Method: Directly embed the API key as a string literal in your code.
Pros: None.
Cons: Extremely insecure. Anyone can decompile your app and get the key. Never hardcode sensitive information.

## What is SSL pinning?
SSL pinning is a security technique used in mobile app development to enhance the security of HTTPS connections. 
It involves "pinning" the app to a specific certificate or public key of the server it communicates with.

### Working:
Obtain Server Certificate: During development, obtain the server's SSL certificate or public key.
Embed Certificate/Key in App: Include the certificate or public key in your app's code (e.g., as a base64-encoded string or in a resource file).
Certificate/Key Verification: When the app makes an HTTPS connection, it compares the server's presented certificate with the pinned certificate/key.
Connection Validation: If the certificates/keys match, the connection is considered trusted. If they don't match, the connection is rejected, preventing potential man-in-the-middle (MitM) attacks.

### Pros:
Strong MitM Protection: Effectively prevents attacks where an attacker intercepts traffic using a forged certificate.
Increased Trust: Provides a higher level of assurance that the app is communicating with the intended server.

### Cons
Certificate Management: Requires careful management of pinned certificates. If the server's certificate changes (e.g., due to renewal), the app may break unless the pinned certificate is updated.
Increased Development Complexity: Adds complexity to the app's networking code.

### Recommendations:
Consider the Risk: Evaluate the sensitivity of the data your app handles and the potential impact of a MitM attack.
Use Sparingly: Pin only to critical endpoints or domains.
Implement Certificate Updates: Have a mechanism to update pinned certificates (e.g., through app updates or a dynamic pinning solution).
Thorough Testing: Test your pinning implementation rigorously to avoid unintended consequences.

### Alternatives:
Public Key Pinning: Pinning to the server's public key is generally more flexible than certificate pinning.



understanding of security mechanisms, like 
Android KeyStore, 
encryption, 
SSL pinning and hardening (ProGuard, DexGuard or similar)