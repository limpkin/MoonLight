# Gemini Notes: ESP32-sveltekit

This file contains notes generated by Gemini to understand the repository and assist with development.

## Project Overview

This is a framework for building IoT projects using the ESP32 microcontroller. It aims to simplify the development of ESP32-based devices by offering:

- **Hardware:** ESP32 (supports multiple variants like S3 and C3).
- **Backend:** C++ based on the Arduino framework, built with PlatformIO. The backend provides services for WiFi management, user authentication, MQTT, RESTful API, and more.
- **Frontend:** A modern web interface built with SvelteKit, styled with TailwindCSS and DaisyUI. The frontend is served directly from the ESP32.
- **Build System:** PlatformIO is used to build the backend C++ code. The build process also includes steps to build the SvelteKit frontend and package it for deployment to the ESP32's flash memory.
- **Documentation:** The project uses `mkdocs` for documentation.
- **A feature-rich backend:** Handling common IoT functionalities like WiFi connectivity, MQTT communication, user authentication, and device management.
- **A modern and responsive web interface:** Allowing users to control and monitor their ESP32 devices through a user-friendly web application accessible from various devices (desktop, mobile).
- **Extensibility:** Designed to be easily adaptable and expandable for various IoT applications.

## Project Structure

The project is structured into three main parts:

- **`src/`**: Contains the core C++ application code for the ESP32. This includes services for MQTT, light state management, and the main application entry point (`main.cpp`).
- **`lib/`**: Houses the C++ libraries and dependencies. This is further divided into:
  - **`framework/`**: A comprehensive set of services and utilities for ESP32 development, including WiFi management (AP, STA, Scanner, Status), MQTT (Settings, Status), NTP (Settings, Status), Security (Authentication, JWT), Firmware Update (Upload, Download), System Status, Sleep, Battery, Analytics, and Core Dump. It also contains core components like `StatefulService`, `HttpEndpoint`, `EventSocket`, `FSPersistence`, and `WebSocketServer`.
  - **`PsychicHttp/`**: A custom HTTP server library for ESP32, providing functionalities for handling HTTP requests, responses, web sockets, and file serving.
- **`interface/`**: This directory holds the SvelteKit frontend application. It's a modern web UI built with SvelteKit, TailwindCSS, and DaisyUI. Key subdirectories include:
  - **`src/`**: Contains the Svelte components, stores (for state management), and routes for the different pages of the web interface.
  - **`static/`**: Static assets like `favicon.png`.
  - Configuration files like `package.json`, `svelte.config.js`, `vite.config.ts`, and `tsconfig.json` define the frontend build process, dependencies, and development setup.

## Key Files and Their Purposes

- **`src/main.cpp`**: The entry point of the ESP32 firmware, responsible for initializing and orchestrating various services.
- **`src/LightMqttSettingsService.h`/`.cpp` and `src/LightStateService.h`/`.cpp`**: Example services demonstrating how to integrate custom device logic (controlling an LED) with MQTT and the web interface.
- **`lib/framework/*.h`/`.cpp`**: These files define the core functionalities and services of the ESP32 backend, covering a wide range of IoT needs.
- **`lib/PsychicHttp/src/*.h`/`.cpp`**: This custom library provides the HTTP server capabilities, enabling the ESP32 to serve the web interface and handle REST API calls and WebSockets.
- **`interface/package.json`**: Defines frontend dependencies (SvelteKit, TailwindCSS, DaisyUI, etc.) and build scripts.
- **`interface/svelte.config.js`**: SvelteKit configuration, including the static adapter for serving the frontend from the ESP32.
- **`interface/vite.config.ts`**: Vite (frontend build tool) configuration, including proxy settings for development and a custom plugin for LittleFS compatibility.
- **`interface/src/routes/`**: Contains the Svelte components for each page of the web application (e.g., `login.svelte`, `menu.svelte`, `wifi/sta/Wifi.svelte`).
- **`interface/src/lib/stores/`**: Svelte stores for managing global application state (e.g., user authentication, telemetry data).
- **`interface/src/lib/components/`**: Reusable Svelte components used across the application.
- **`interface/src/lib/types/models.ts`**: TypeScript type definitions for data models used in the frontend, ensuring type safety when interacting with the backend API.

## Important Dependencies

- **Backend (C++)**:
  - **ArduinoJson**: For JSON serialization and deserialization.
    - Context7 ID: `/bblanchon/arduinojson`
  - **PsychicHttp**: Custom HTTP server library.
  - **PsychicMqttClient**: MQTT client library.
  - **LittleFS**: Filesystem for ESP32.
    - Context7 ID: `/jilio/sqlitefs` (Note: This is the closest match found for a filesystem library, but it's for Go and SQLite. The actual LittleFS library for ESP32 might not have a direct Context7 ID.)
  - **WiFi, ESPmDNS, time.h, lwip/apps/sntp.h**: Standard ESP-IDF and Arduino libraries for networking and time.
- **Frontend (SvelteKit)**:
  - **SvelteKit**: The web framework.
  - **TailwindCSS**: CSS framework for styling.
  - **DaisyUI**: TailwindCSS component library for UI elements.
  - **unplugin-icons**: For using icon sets.
  - **jwt-decode**: For decoding JWT tokens.
  - **msgpack-lite**: For MessagePack serialization/deserialization (used for WebSocket communication).
  - **chart.js, chartjs-adapter-luxon, luxon**: For charting and date/time handling in analytics.
  - **svelte-dnd-list, svelte-modals**: UI components.

## Important Configuration Files

- **`platformio.ini`**: PlatformIO project configuration for the ESP32 backend, defining build environments, libraries, and upload settings. This file is crucial for the integrated build process.
- **`factory_settings.ini`**: Likely contains default or factory-set values for various ESP32 parameters.
- **`features.ini`**: Probably defines which features (e.g., MQTT, Security, Sleep) are enabled or disabled in the firmware, allowing for modular builds.
- **`mkdocs.yml`**: Configuration for the project's documentation site.
- **`interface/vite.config.ts`**: Contains proxy settings for development, allowing the frontend to communicate with a running ESP32 backend during development. This is important for local development workflow.

## Development Workflow

1.  **Backend Development:** Modify the C++ files in the `src/` and `lib/` directories. Build and upload to the ESP32 using PlatformIO.
2.  **Frontend Development:** Work within the `interface/` directory. Use `npm` to manage dependencies and run the SvelteKit development server.
3.  **Building for Production:** The PlatformIO build process (`platformio run`) will automatically build the frontend and embed it into the firmware or a separate flash partition.

## Notes for Gemini

- When asked to modify the frontend, I will work in the `interface/` directory.
- When asked to modify the backend, I will work in the `src/` and `lib/` directories.
- I should be aware of the build process and how the frontend and backend are integrated.
- I will use the existing libraries and frameworks when adding new features.
- Do use `fetch` to retrieve data from the backend.
- Use the context7 MCP to access documentations of libraries and frameworks.
- Assume that the vite dev server (`npm run dev`) is already running.
