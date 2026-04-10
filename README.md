# Volatility3-forensics

## Objective
Perform memory forensics on two compromised Windows systems — a banking
trojan (Case 001, Windows XP) and a WannaCry ransomware infection (Case 002)
— using Volatility3 plugins and string extraction to reconstruct the full
attack chain from RAM dumps alone.

### Skills Learned
- Memory acquisition methods: FTK Imager, Redline, DumpIt, Memoryze
- Virtual machine memory formats: .vmem, .bin, .mem, .sav
- Host identification: windows.info (build version, acquisition time)
- Process enumeration: pslist, psscan, pstree — evasion differences
- Network connection analysis: netstat, netscan
- DLL enumeration per process: dlllist
- Injected code detection: malfind (RWE/RX flags, MZ header)
- YARA scanning in memory: yarascan
- Process memory dump + string extraction:
  memmap.Memmap --pid + strings + grep
- SSDT hook detection for rootkit analysis: ssdt
- Kernel module and driver enumeration: modules, driverscan
- Mutex identification as malware IOC: handles
- File scanning from malware directory: filescan

### Tools Used
- Volatility3 (python3 vol.py)
- strings + grep (Linux CLI)
- Memory files: Investigation-1.vmem, Investigation-2.raw

## Steps
**Case 001 — Banking Trojan:**
`windows.info` → build `2600.xpsp.080413-2111`, acquired `2012-07-22 02:45:08`.
`windows.pstree` → `reader_sl.exe` (PID 1640) under explorer.exe (PID 1484).
Process memory dumped, strings grepped for "user-agent" →
`Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)`.
Chase Bank confirmed among targeted domains.

**Case 002 — WannaCry:**
`windows.pstree` → `@WanaDecryptor@.exe` (PID 740) under tasksche.exe
(PID 1940). Full path: `C:\Intel\ivecuqmanpnirkt615\@WanaDecryptor@.exe`.
`dlllist` → `ws2_32.dll` loaded (C2 socket capability confirmed).
`handles` → mutex `MsWinZonesCacheCounterMutexA` (known WannaCry IOC).
`filescan` identified as plugin for malware directory file enumeration.

*Ref 1: pstree — reader_sl.exe (Case 001) and @WanaDecryptor@.exe (Case 002)*
*Ref 2: Process dump + grep — user-agent extraction and WannaCry binary path*
