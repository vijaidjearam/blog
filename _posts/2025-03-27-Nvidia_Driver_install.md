---
layout: post
date: 2025-03-27 12:45:09
title: Nvidia Driver install
category: nvidia
tags: nvidia windows driver
---

## Documentation: Clean Nvidia Driver Installation using DDU and NVCleanInstall

This document outlines how to thoroughly remove existing Nvidia graphics drivers using Display Driver Uninstaller (DDU) and then perform a clean, minimal installation of new drivers using NVCleanInstall. This method allows you to install only the necessary components and avoid unwanted features like telemetry and the Nvidia App (formerly GeForce Experience).

### Part 1: Uninstalling Nvidia Drivers with Display Driver Uninstaller (DDU)

**Display Driver Uninstaller (DDU)** is a **free tool** designed to completely remove graphics card drivers from your system. It is useful for ensuring a clean slate before installing new drivers, particularly when experiencing driver-related issues such as **blue screen errors**, display problems (freezing, black screens), or installation failures.

**Steps for Using DDU:**

1.  **Download DDU** from the provided (link)[https://www.wagnardsoft.com/display-driver-uninstaller-DDU-].
2.  Once the download is complete, **extract the contents** of the DDU archive to a convenient location on your computer (e.g., a folder on your desktop).
3.  For the most effective driver removal, it is **strongly recommended to boot your computer into Safe Mode**. Consult a guide specific to your Windows version for instructions on how to do this.
4.  After your computer has started in Safe Mode, navigate to the folder where you extracted DDU and **run the Display Driver Uninstaller application**.
5.  Within the DDU interface, on the right-hand side, locate the drop-down menus. Select **"GPU"** for the device type and **"Nvidia"** for the brand.
6.  In the top-left corner of the DDU window, click the button labelled **"Clean and restart"**.
7.  DDU will now proceed to remove the Nvidia drivers from your system. Once the process is finished, your computer will **automatically restart**.
8.  After the restart and upon returning to normal Windows, you can optionally verify that the drivers have been uninstalled. Open **Device Manager** (search for it in the Start Menu), expand the **"Display adapters"** section, and your Nvidia graphics card should be listed with a basic Microsoft display driver or a generic name. The driver details should indicate Microsoft as the provider.

### Part 2: Installing Nvidia Drivers with NVCleanInstall

**NVCleanInstall** is a **free utility** that facilitates a **minimal installation** of Nvidia drivers. It provides control over which driver components are installed, allowing you to exclude telemetry and the Nvidia App. It can work with the latest drivers downloaded by the tool or with driver installation files you have manually downloaded from the official Nvidia website.

**Steps for Using NVCleanInstall:**

1.  It is **highly recommended to run DDU (as described in Part 1) before using NVCleanInstall** to ensure a clean driver environment.
2.  **Download NVCleanInstall** from the provided link.
3.  **NVCleanInstall is a portable application**, meaning you can simply run the downloaded executable file without needing to install it.
4.  When you launch NVCleanInstall, you will be presented with options for selecting the Nvidia driver. You can either choose to have NVCleanInstall **download the latest recommended drivers for your hardware** or **select an existing driver installation file** that you have already downloaded (usually a `.exe` file). Choose your preferred method and click **"Next"**.
5.  NVCleanInstall will then display a list of Nvidia driver components that you can select for installation. By default, only the core **Display Driver** will be chosen. You will need to **manually select any additional components** you wish to install. Consider the following:
    *   **Display Driver:** This is **essential** for your graphics card to function.
    *   **HD Audio via HDMI:** Select this only if you connect your display via HDMI and use it for audio output.
    *   **PhysX System Software:** This is for physics acceleration in older games that support PhysX. Note that it is less relevant for modern games.
    *   **Legacy Control Panel:** This is the standard Nvidia Control Panel and is **strongly recommended** for managing graphics settings.
    *   **Microsoft Visual C++ Redistributables:** While often already installed, selecting these can help prevent potential compatibility issues.
    *   **USB-C Driver:** Only select this if your graphics card has a USB-C port.
    *   **Nvidia DLSR:** This feature enables image upscaling using AI and is generally recommended as it does not typically impact performance negatively.
    *   **Telemetry** and **Nvidia GeForce Experience (Nvidia App)** are **not selected by default**, allowing for a cleaner installation without these components. An alternative tool, **Nvidia Profile Inspector**, can be used for more advanced driver configuration.
    *   Carefully review the description of each component and select only those you need.
6.  After making your selections, click **"Next"**. NVCleanInstall will then create a **custom Nvidia driver installer** containing only the components you have chosen. You might encounter "Copy Failed" messages during this process; if so, try clicking the "Retry" button.
7.  Once the custom installer is prepared, it will **automatically launch the standard Nvidia driver installation process**. Follow the on-screen prompts to complete the driver installation.
8.  When prompted, **restart your computer** to finalise the installation.
