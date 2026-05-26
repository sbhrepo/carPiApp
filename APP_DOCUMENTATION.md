# CarPI Client - App Documentation

## Overview
The CarPI Client project is a B4A (Basic4Android) Android application designed to remotely control a Raspberry Pi-powered car over a local Wi-Fi network. It achieves this by calling a series of HTTP REST-like endpoints hosted on the car's web server.

## 1. UI and Controls (Current State)
The application currently uses a button and form-based layout (`carLayout.bal`) built with standard Android views augmented by XUI views.

### Input Parameters
*   **`baseURL`**: Defines the IP and port of the Raspberry Pi server (e.g., `http://192.168.1.105:8090/`). 
*   **`Speed`**: Sets the desired motor speed for movement commands.
*   **`EdtTTime` (Turn Time)**: Specifies how long the car will turn before returning to a straight path or stopping.
*   **`Repitition`**: Specifies the number of looping iterations for a saved macro/recording.

### Action Buttons
*   **Drive Controls:** `Forward`, `Backward`, `TurnLeft`, `TurnRight`, `Left` (Shift), `Right` (Shift), and `Stop`. 
*   **Admin Controls:** `On`, `Off`, `Shutdown`, and `Status` check.
*   **Recording Controls:** Macro capability to sequence movements via `StartRecording`, `StopRecording`, `CancelRecording`, and `RefreshRecording`.
*   **Macro List (`Recordings`)**: A ListView displaying fetched macros, ready to execute on click.

## 2. API Integration (REST Interface)
Remote execution relies heavily on `okhttputils2` (specifically, `HttpJob` objects). 
The core subroutine `restCall` issues HTTP GET commands appended to the `baseURL`, while `restCallback` digests the plaintext responses (and displays them to `ResultsLabel` or in `Msgbox` popups on failure).

### Supported Endpoints
**Drive Endpoints (Movement)**
*   `[GET] /drive/stop` : Halts all motors immediately.
*   `[GET] /drive/front/{Speed}` : Drive forward.
*   `[GET] /drive/back/{Speed}` : Drive reverse.
*   `[GET] /drive/right/{Speed}` : Shift/Strafe right.
*   `[GET] /drive/left/{Speed}` : Shift/Strafe left.
*   `[GET] /drive/turnRight/{Speed}/{Time}` : Turn right dynamically based on specified duration.
*   `[GET] /drive/turnLeft/{Speed}/{Time}` : Turn left dynamically based on specified duration.

**Admin Endpoints (Power Management)**
*   `[GET] /control/poweron` : Engage subsystems.
*   `[GET] /control/poweroff` : Disengage subsystems.
*   `[GET] /control/shutdown` : Initiates safety shutdown of the Raspberry Pi backend.
*   `[GET] /status` : Returns current connectivity/state diagnostics.

**Recording Endpoints (Macros)**
*   `[GET] /recording/start/{RecName}` : Begin recording car inputs.
*   `[GET] /recording/stop` : Save the current recording buffer.
*   `[GET] /recording/cancel` : Discard the current recording buffer.
*   `[GET] /recordings` : Returns a space-separated string of saved filenames/macros.
*   `[GET] /recording/play/{RecName}/{Repititions}` : Replays a specific saved sequence.

## 3. Configuration & Build Specifics
*   **IDE Framework:** B4A (Basic4Android) v12.8.
*   **App Dependencies / Libraries:** `core`, `json`, `okhttputils2`, `xui`, `xui views`.
*   **Application Manifest Properties:**
    *   Minimum SDK: `14`
    *   Target SDK: `30`
    *   Orientation: Locked natively to `portrait`.
    *   Network Policy: HTTP traffic permitted natively (via `android:usesCleartextTraffic="true"`).
*   **Compilation / Build:** Build is handled organically through the B4A Desktop IDE (connecting via B4A-Bridge to a physical device or an emulator). The project compiles Java byte code down to `.dex` inside the generated `Objects\bin\` folders, mapping layout coordinates statically through the IDE's designer tool mapping (e.g., `designerscripts/LS_carlayout.java`).