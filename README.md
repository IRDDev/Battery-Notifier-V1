# Battery Notifier V1

A simple Windows battery monitoring and notification utility built with Python.

## Overview

Battery Notifier V1 monitors battery charge levels and provides audible notifications when the battery reaches configurable full or low battery thresholds.

The application runs in the background and provides a system tray icon for accessing settings or exiting the application.

## Features

- Audible notification when the battery reaches the configured full battery threshold while charging.
- Audible notification when the battery reaches the configured low battery threshold while unplugged.
- Configurable full and low battery thresholds.
- Input validation for battery threshold settings.
- System tray menu for opening settings or exiting the application.
- Closing the settings window hides the application in the system tray.
- Background battery monitoring at three-second intervals.

## Built With

- Python 3
- Tkinter
- psutil
- pystray
- Pillow
- winsound

## Requirements

- Windows
- Python 3.x
- The required third-party Python packages

## Installation

Install the required third-party packages:

```bash
python -m pip install psutil pystray Pillow
```

## Run the Application

Run the following command in the project directory:

```bash
python simple_battery_notifier.py
```

## Configuration

The default thresholds are defined at the beginning of `simple_battery_notifier.py`:

- Full Battery: 100%
- Low Battery: 20%

You can change these thresholds in the application window and select **Save Settings** to apply them.

The current implementation applies settings during the running session. Settings are not saved permanently between application launches.

## Project Background

This project was developed as a practical desktop utility to provide audible battery-level notifications without requiring continuous attention to the battery indicator.

## Project Status

Version 1 — Basic battery monitoring with configurable thresholds and system tray support.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
