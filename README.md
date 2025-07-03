# MobSF-modAVD
A modified version of Docker MobSF v4.4.0, created with the goal to make it simpler to use with Android Studio AVDs in Dynamic Analysis Mode.

## My setup

- Windows 11 Host
(Running Android Studio Narwhal, Build #AI-251.25410.109.2511.13665796)
- Kali WSL2 Virtual Machine, in `networkingMode=mirrored`
(Running the Docker instance of MobSF)

## Step-by-step

1. [Create an Android AVD](https://developer.android.com/studio/run/managing-avds) -- Do not boot the device yet!<br/>
API<=30<br/>
Services: Android Open Source (AOS)

2. Close everything (Android Studio, Virtual Device Manager).
   
3. Start PowerShell as Administrator, and execute the following commands:
```powershell
Set-ExecutionPolicy Unrestricted -Scope CurrentUser
.\start_avd.ps1 -List_AVDS
.\script.ps1 -AVD_NAME <your-listed-avd-name>
```

4. Let it simmer for ~3 minutes. You are expecting a message, saying "Emulator ready for Dynamic Analysis with MobSF.".

5. Ensure your WSL2 machine is running in `networkingMode=mirrored`<br/>https://learn.microsoft.com/en-us/windows/wsl/networking#mirrored-mode-networking

6. Start a MobSF Docker container:
```bash
docker run -it --rm --add-host=host.docker.internal:host-gateway -e MOBSF_ANALYZER_IDENTIFIER=emulator-5554 --net host umbradeorum/mobile-security-framework-mobsf-modavd:latest
```
Some of the above flags will not find actual use in this modified version, but this may somewhat ensure that the same command will also work with any future updates.

7. Login with default MobSF credentials `mobsf:mobsf`.

8. Navigate to the Dynamic Analyzer tab, and then to Android Dynamic Analyzer.
  
9. It should work out-of-the-box. At least, it does for me!<br/>
In case it did not, try to select the `MobSFy Android Runtime` option, and then `MobSFy`. Don't mind the value -- it is ignored in this version.<br/>
If you installed an application after completing the aforementioned steps (`adb install <apk-name-in-folder>`), reload the `Dynamic Analyzer` page.

---

## Installation of Docker image

### Option 1: Modify the original MobSF Docker image

1. Pull the image's latest version (this modification was tested on MobSF v4.4.0)
```bash
docker pull opensecurity/mobile-security-framework-mobsf:latest 
```

2. Run it and take note of its **Container ID**
```bash
docker run -it --rm --add-host=host.docker.internal:host-gateway -e MOBSF_ANALYZER_IDENTIFIER=emulator-5554 --net host opensecurity/mobile-security-framework-mobsf:latest
docker ps
```

3. Push the modfiles into their respective locations, as per the **Patchfile map**.
```bash
docker cp <patchfile> <containter-id>:<destination>
```

4. Commit locally for future use:
```bash
docker commit -a umbradeorm <container_NAME> umbradeorum/mobile-security-framework-mobsf-modavd:latest
```

5. From now on, you can run it via:
```bash
docker run -it --rm --add-host=host.docker.internal:host-gateway -e MOBSF_ANALYZER_IDENTIFIER=emulator-5554 --net host umbradeorum/mobile-security-framework-mobsf-modavd:latest
```

### Option 2: Load the ready Docker image archive

1. From the **Releases** tab, download the latest `MobSF-modAVD-vX.X.tar.7z` file.

2. Verify and extract to `.tar`:
```bash
7z l MobSF-modAVD-vX.X.tar.7z
7z x MobSF-modAVD-vX.X.tar.7z -o <output_folder>
```

3. Load the `.tar` archive as a Docker image:
```bash
docker load < MobSF-modAVD-vX.X.tar
```

4. From now on, you can run it via:
```bash
docker run -it --rm --add-host=host.docker.internal:host-gateway -e MOBSF_ANALYZER_IDENTIFIER=emulator-5554 --net host umbradeorum/mobile-security-framework-mobsf-modavd:latest
```

---

## Patchfile map

| Patchfile | Destination |
|:-:|:-:|
| [environment.py](https://github.com/UmbraDeorum/MobSF-modAVD/blob/main/mobsf-android-patchfiles/environment.py) | /home/mobsf/Mobile-Security-Framework-MobSF/mobsf/DynamicAnalyzer/views/android/environment.py |
| [frida_core.py](https://github.com/UmbraDeorum/MobSF-modAVD/blob/main/mobsf-android-patchfiles/frida_core.py) | /home/mobsf/Mobile-Security-Framework-MobSF/mobsf/DynamicAnalyzer/views/android/frida_core.py |
| [utils.py](https://github.com/UmbraDeorum/MobSF-modAVD/blob/main/mobsf-android-patchfiles/utils.py) | /home/mobsf/Mobile-Security-Framework-MobSF/mobsf/MobSF/utils.py |

---

## Uninstallation / Reverting to the original

Since we are working on Docker containers, to remove the modified version:

1. List existing docker images:
```bash
docker image list 
```

2. Remove the loaded docker image:
```bash
docker image rm umbradeorum/mobile-security-framework-mobsf-modavd:latest
```


Also, if you want to then revert to using the original (and soon to be newer) version of MobSF:


1. Pull the latest MobSF docker image:
```bash
docker pull opensecurity/mobile-security-framework-mobsf:latest
```

2. Follow the original documentation:
* [Running MobSF](https://mobsf.github.io/docs/#/running_mobsf_docker)
* [MobSF - Android Studio Emulator Guide](https://mobsf.github.io/docs/#/dynamic_analyzer_docker?id=android-studio-emulator)

---

### DISCLAIMER

Be your own you, but also be responsibe.<br/>
This tool has been widely used to make the world a safer place.<br/>If you abuse it to illegal means, be ready to own up to it! <br/>
This modification aims to make an Offensive Security Consultant's life a bit easier, and a student's entry point to learning a bit less thorny.<br/>
That's all!

---

<br/>

> This project is licensed under the terms of the GNU GENERAL PUBLIC LICENSE (Version 3, 29 June 2007).
