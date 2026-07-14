# Get the Computer Name – Memory Forensics

## Objective
To analyze a memory dump file using memory forensics techniques in order to identify the hostname of a workstation from volatile memory.

## Scenario
During an investigation, a memory dump of a workstation was obtained. However, the system's hostname was not recorded at the time of acquisition. The objective was to analyze the memory dump using forensic tools to recover the computer name stored within system memory.

## Tools Used
- **Volatility Framework 2.6.1** – Memory forensic analysis tool
- **Kali Linux VM** – Analysis environment
- **imageinfo plugin** – To determine the correct Windows memory profile
- **hivelist plugin** – To locate registry hive addresses in memory
- **printkey plugin** – To extract registry key values from memory
- **Memory Dump File:** `memdump.dmp`

## Methodology
1. Loaded the memory dump file `memdump.dmp` into the Volatility framework
2. Executed the `imageinfo` plugin to determine the appropriate Windows profile

```bash
volatility -f memdump.dmp imageinfo
```

> Profile identified: **Win7SP1x86**

3. Executed the `hivelist` plugin to identify registry hive locations in memory

```bash
volatility -f memdump.dmp --profile=Win7SP1x86 hivelist
```

4. Identified the **SYSTEM** registry hive at offset `0x8b21c008`
5. Executed the `printkey` plugin using the identified offset to query the ComputerName registry key

```bash
volatility -f memdump.dmp --profile=Win7SP1x86 printkey -o 0x8b21c008 -K "ControlSet001\Control\ComputerName\ComputerName"
```

6. Successfully retrieved the computer name from the registry

## Analysis & Findings
The registry analysis revealed that the SYSTEM hive contained the key responsible for storing the system hostname. Using the `printkey` plugin, the following registry key was examined:

```
ControlSet001\Control\ComputerName\ComputerName
```

The retrieved registry value confirmed the system hostname as:

**`WIN-ETSA91RKCFP`**

## Screenshots
> <img width="1019" height="562" alt="Screenshot 2026-04-12 154752" src="https://github.com/user-attachments/assets/9cbe24b6-2842-4d88-9208-9032d45206b8" />
<img width="572" height="202" alt="Screenshot 2026-04-12 155053" src="https://github.com/user-attachments/assets/27dd8155-14b7-4a94-8810-60b306cf4ce7" />
> <img width="790" height="568" alt="Screenshot 2026-04-16 151828" src="https://github.com/user-attachments/assets/5806232c-5bf9-4293-bfce-18544cde3ca0" />
> <img width="727" height="571" alt="Screenshot 2026-04-16 152016" src="https://github.com/user-attachments/assets/cd13fa2d-1416-427f-a890-e906f7aa5aa8" />
> <img width="903" height="531" alt="Screenshot 2026-04-16 153226" src="https://github.com/user-attachments/assets/e2302b65-0eb6-4979-9264-594d65a51e24" />


## Conclusion
The forensic analysis successfully retrieved the computer name from the memory dump using Volatility registry analysis. By identifying the SYSTEM hive offset and querying the ComputerName registry key, the hostname **WIN-ETSA91RKCFP** was recovered without requiring access to the live system.

## Recommendations
- Always record system information (hostname, IP, OS version) before memory acquisition
- Use standardized memory forensic procedures to ensure repeatability
- Maintain secure storage of memory dump files to preserve evidence integrity

---
*Part of the Cyber50 Defense Blue Team Internship Program – Digital Forensics module.*
