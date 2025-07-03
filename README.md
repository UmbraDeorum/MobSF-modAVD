# MobSF-modAVD
A modified version of MobSF v4.4.0, created with the goal to make it simpler to use with Android Studio AVDs in Dynamic Analysis Mode.

My setup:

- Windows 11 Host
(Running Android Studio Narwhal, Build #AI-251.25410.109.2511.13665796)
- Kali WSL2 Virtual Machine, in networkingMode=mirrored
(Running the Docker instance of MobSF)

Step-by-step:

1. Create an Android AVD, of API>=30 (Services: Android Open Source [AOS])

2. Start PowerShell as Administrator, and execute the following commands:
```powershell
Set-ExecutionPolicy Unrestricted -Scope CurrentUser
.\start_avd.ps1 -List_AVDS
.\script.ps1 -AVD_NAME <your-listed-avd-name>
```

3. Let it simmer for ~3 minutes. You are expecting a message, saying "Emulator ready for Dynamic Analysis with MobSF.".

4. Ensure your WSL2 machine is running in `networkingMode=mirrored`<br/>https://learn.microsoft.com/en-us/windows/wsl/networking#mirrored-mode-networking

5. Start a MobSF Docker container:
```bash
sudo docker run -it --rm --add-host=host.docker.internal:host-gateway -e MOBSF_ANALYZER_IDENTIFIER=emulator-5554 --net host umbradeorum/mobile-security-framework-mobsf-modavd:latest
```
Some of the above flags will not find actual use in this modified version, but this may somewhat ensure that the same command will also work with any future updates.

6. Login with default MobSF credentials `mobsf:mobsf`.

7. Navigate to the Dynamic Analyzer tab, and then to Android Dynamic Analyzer.
8. It should work out-of-the-box. At least, it does for me!<br/>
In case it did not, try to select the `MobSFy Android Runtime` option, and then `MobSFy`. Don't mind the value -- it is ignored in this version.<br/>
If you installed an application after completing the aforementioned steps (`adb install <apk-name-in-folder>`), reload the `Dynamic Analyzer` page.



