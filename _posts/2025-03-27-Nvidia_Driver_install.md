---
layout: post
date: 2025-03-27 12:45:09
title: Nvidia Driver install
category: nvidia
tags: nvidia windows driver
---

## Documentation: Using DDU to Uninstall Nvidia Drivers and Installing NVCleanInstall

This document outlines the steps to completely uninstall your Nvidia graphics drivers using Display Driver Uninstaller (DDU) and subsequently install NVCleanInstall for a minimal and telemetry-free Nvidia driver installation.

### Part 1: Uninstalling Nvidia Drivers with Display Driver Uninstaller (DDU)

Display Driver Uninstaller (DDU) is a **free utility** designed to thoroughly remove graphics card drivers, including any residual files or registry entries [1, 2]. This is particularly useful when experiencing driver-related issues like **BSODs (e.g., 0x116, VIDEO_TDR_ERROR)**, display problems (freezes, black screens), or Nvidia installer errors [3].

**Steps to Use DDU:**

1.  **Download DDU:**
    *   Navigate to the official DDU download link provided in the source: [Télécharger DDU – Display Driver Uninstaller] [4].

2.  **Decompress DDU:**
    *   Once downloaded, DDU will be in a self-extracting archive [4].
    *   Run the executable and specify a destination folder, for example, your Desktop in a folder named "DDU" [4].

3.  **Boot into Safe Mode:**
    *   It is **strongly recommended** to run DDU in Safe Mode to ensure the most complete removal of drivers [4, 5].
    *   Follow a guide on how to start Windows in Safe Mode. A link is provided in the source: [Démarrez Windows en mode sans échec] [6].

4.  **Run Display Driver Uninstaller (DDU):**
    *   Once in Safe Mode, navigate to the folder where you extracted DDU and run the `Display Driver Uninstaller.exe` file [6].

5.  **Select GPU and Nvidia:**
    *   In the DDU interface, on the right-hand side, use the drop-down menus to select:
        *   **Device type:** `GPU` [6].
        *   **Select driver:** `Nvidia` [6].

6.  **Clean and Restart:**
    *   At the top left of the DDU window, click on the button labelled **"Nettoyer et redémarrer"** (Clean and restart) [6].
    *   DDU will now proceed to remove the Nvidia drivers and automatically restart your computer once the process is complete [7].

7.  **Verify Uninstallation (Optional):**
    *   After the restart, once you are back in normal Windows, you can verify that the drivers have been uninstalled.
    *   Open **Device Manager** (search for it in the Start Menu).
    *   Expand the **"Display adapters"** category.
    *   Your Nvidia graphics card should now be listed with a basic Microsoft display adapter driver (or a generic name) [7]. In the "Drivers" tab of the device properties, the driver provider should be Microsoft [7].

### Part 2: Installing NVCleanInstall

NVCleanInstall is a **free utility** that allows for a **minimal installation** of Nvidia drivers, giving you control over which components are installed and enabling the exclusion of telemetry and Nvidia GeForce Experience [8]. It can work with the latest drivers downloaded by the tool itself or with manually downloaded driver packages [9, 10].

**Steps to Install NVCleanInstall:**

1.  **Clean Nvidia Components (Recommended):**
    *   It is highly recommended to run **DDU as described in Part 1** before using NVCleanInstall to ensure a clean base for the new driver installation [5, 10].

2.  **Download NVCleanInstall:**
    *   Download the NVCleanInstall utility from the link provided in the source: [Télécharger NVCleanInstall] [10].
    *   NVCleanInstall is a portable application, meaning it **does not require installation** [9].

3.  **Run NVCleanInstall:**
    *   Once downloaded, simply run the `NVCleanstall.exe` file [10].

4.  **Select Nvidia Driver to Install:**
    *   NVCleanInstall presents options for selecting the driver. You can either:
        *   Choose **"Install best drivers for my hardware"** to have NVCleanInstall download the latest recommended drivers [10].
        *   Select **"Use driver files on disk"** if you have already downloaded a specific Nvidia driver package from the official Nvidia website (as mentioned in the YouTube source [11]). Browse to the location of the downloaded `.exe` file [9, 10].
    *   Click **"Next"** [10].

5.  **Choose Components to Install:**
    *   NVCleanInstall will display a list of Nvidia driver components that you can select for installation [12].
    *   By default, only the core display driver is selected. You will need to **manually check** the components you wish to install, such as:
        *   **Display Driver:** This is **essential** for your graphics card to function [13].
        *   **HD Audio via HDMI:** Only if you use HDMI for audio output [14, 15].
        *   **PhysX System Software:** For physics acceleration in older games that support it [14, 16, 17]. The YouTube source suggests this is mostly for older games [17].
        *   **Legacy Control Panel:** This is the standard Nvidia Control Panel and is **strongly recommended** to be selected [15].
        *   **Microsoft Visual C++ Redistributables:** The YouTube source suggests these are usually already installed but recommends checking them to avoid potential bugs [16].
        *   **USB-C Driver:** Only if your graphics card has a USB-C port [17].
        *   **Nvidia DLSR:** For upscaling using deep learning; the YouTube source recommends installing it as it doesn't impact performance [18, 19].
    *   **Telemetry** and **GeForce Experience** are **not selected by default**, allowing for a cleaner installation [8, 12].
    *   The YouTube source mentions **NVidia Profile Inspector** as a more efficient alternative to Nvidia App and GeForce Experience for configuration [14, 20]. This is a separate tool and not installed through NVCleanInstall.
    *   Review the descriptions for each component and select according to your needs [9].

6.  **Create Custom Installer:**
    *   After selecting your desired components, click **"Next"** [10].
    *   NVCleanInstall will then create a **customised Nvidia driver installer** with only the selected components [12]. You might encounter "Copy Failed" errors during this process, in which case the source suggests clicking "Recommencer" (Retry) [12].

7.  **Install the Cleaned Nvidia Drivers:**
    *   Once the custom installer is created, it will automatically launch the standard Nvidia driver installation process [21].
    *   Follow the on-screen prompts to complete the driver installation [21].
    *   Restart your computer when prompted [22].
