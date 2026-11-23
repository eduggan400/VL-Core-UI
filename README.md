# REV Control Hub — Ultra Dark Glass Console

A custom, offline-capable web dashboard for the **FIRST Tech Challenge (FTC) REV Control Hub**. Built with a sleek dark glassmorphism aesthetic, it runs directly on the Control Hub alongside the official REV software (`:8080`) on port `:8081`—allowing anyone connected to the robot's Wi-Fi network to view live telemetry without browser extensions or internet access.

---

## ✨ Features

* **Ultra-Dark Glassmorphism UI:** High-contrast emerald and obsidian interface with CSS backdrop blur and subtle pulse animations.
* **100% Offline Ready:** Bundles a standalone `tailwind.min.css` stylesheet directly inside the Android APK assets so styling never breaks on the internet-isolated Control Hub Wi-Fi.
* **Live Robot Telemetry:** Polls a lightweight `NanoHTTPD` JSON endpoint every `500ms` to display real-time battery voltage, voltage bar level, active OpMode status, warnings, and connected hardware nodes.
* **Zero-Extension Network Access:** Hosted directly on the Control Hub at `http://192.168.43.1:8081` so any phone, tablet, or pit laptop can view it natively.
* **Full Stock Compatibility:** Leaves the official REV web interface (`192.168.43.1:8080`) untouched for field inspections, while linking directly to **Blocks**, **OnBot Java**, and **Manage** tools.

---

## 📁 Project Structure

```text
TeamCode/
└── src/
    └── main/
        ├── assets/
        │   ├── index.html          # Custom Ultra Dark Glass Console UI
        │   └── tailwind.min.css    # Offline compiled Tailwind CSS bundle
        └── java/org/firstinspires/ftc/teamcode/
            ├── CustomDashboardServer.java  # Embedded NanoHTTPD server (Port 8081)
            └── DashboardData.java          # Static telemetry state holder
```

---

## 🚀 Installation & Setup (Android Studio)

### 1. Add Web Assets
1. Switch Android Studio's file tree from **Android** to **Project** view.
2. Navigate to `TeamCode/src/main/` and create a directory named `assets` (if it doesn't already exist).
3. Place `index.html` and `tailwind.min.css` inside `TeamCode/src/main/assets/`.
4. Ensure `index.html` references the local stylesheet in its `<head>`:
   ```html
   <link rel="stylesheet" href="/tailwind.min.css">
   ```

### 2. Configure the Embedded Web Server
1. Add `CustomDashboardServer.java` to your `teamcode` package. It uses the FTC SDK's `@OnCreate` and `@OnDestroy` lifecycle hooks to automatically start a `NanoHTTPD` server on port `8081` when the Robot Controller app boots.
2. Ensure your `serve()` method routes requests properly:
   * `/` → Serves `index.html` (`text/html`)
   * `/tailwind.min.css` → Serves `tailwind.min.css` (`text/css`)
   * `/telemetry` → Returns live JSON robot data (`application/json`)

### 3. Feed Live Telemetry from Your OpModes
Update your `DashboardData` variables inside your `LinearOpMode` or `OpMode` loop:
```java
DashboardData.voltage = hardwareMap.voltageSensor.iterator().next().getVoltage();
DashboardData.opmode = "TELEOP_ACTIVE";
```

### 4. Build & Connect
1. Connect your laptop to the REV Control Hub via USB-C or wireless ADB (`adb connect 192.168.43.1:5555`).
2. Click **Run 'TeamCode'** in Android Studio to deploy the updated APK.
3. Connect any device to your robot's Wi-Fi network and open a browser to:
   ```text
   http://192.168.43.1:8081
   ```

---

## ⚖️ FTC Competition Legality Note
Because this dashboard runs on a secondary port (`:8081`) for pit monitoring and diagnostics while leaving the official FTC Robot Controller software (`:8080`) intact, it does not interfere with standard field management or inspection protocols. Always ensure all autonomous/teleop control strictly adheres to the current season's *Game Manual Part 1* rules regarding driver station communications.