# TIPC supplementary docs

Some additional information on the TIPC that expands on the 1983 Tech Ref as well as the Maintenance Handbook.  
It is based on what's available and supplemented with information derived from the system ROM and testing.

**Note:  
This is a work in progress and may contain errors.  
I’m primarily documenting this for my own reference, but I’m happy to share it with others.**


## System Error Codes – ROM / POST / Maintenance Reference

### The different types of error codes

1. **displayed System/Keyboard error codes**
2. **LED error groups**
3. **parallel test-plug**

Note: the same numeric value can have a different meaning depending on the diagnostic layer.

---

### 1. Displayed Power-Up and Runtime Error Codes

| Code | Kind  | Meaning | Notes |
|---|---|---|---|
| `0001` | System Error | Reserved | Maintenance Handbook Table B-1; meaning not known yet |
| `0002` | System Error | CRT controller failure | Maintenance Handbook Table B-1 |
| `0003` | System Error | Floppy disk controller failure | Maintenance Handbook Table B-1 |
| `0004` | System Error | Interrupt controller / timer-stage failure | ROM groups interrupts and timers in this POST stage |
| `0005` | System Error | RAM failure | Maintenance Handbook Table B-1 |
| `0006` | System Error | ROM failure | Maintenance Handbook Table B-1 |
| `0007` | System Error | Digital system failure | Maintenance Handbook Table B-1 |
| `0008` | System Error | Unexpected NMI during power-up | Handbook / ROM |
| `0009` | System Error | RAM parity error during power-up | Handbook / ROM |
| `0010` | Keyboard Error | Keyboard not installed | Loopback line does not wiggle |
| `0011` | Keyboard Error | Keyboard installed but no response | No response to init command |
| `0012` | Keyboard Error | Keyboard RAM failure | Code transmitted by keyboard |
| `0013` | Keyboard Error | Keyboard ROM failure | Code transmitted by keyboard |
| `0014` | Keyboard Error | Unexpected keyboard response |  |
| `0015` | Keyboard Error | ACK character receive error |  |
| `0020` | System Error | Option RAM bank 1 failure | Handbook / ROM |
| `0021` | System Error | Option RAM bank 2 failure | Handbook / ROM |
| `0022` | System Error | Option RAM bank 3 failure | Handbook / ROM |
| `0023` | System Error / reserved | Option RAM bank 4 failure | Defined in ROM |
| `0024` | System Error / reserved | Option RAM bank 5 failure | Defined in ROM |
| `0025` | System Error / reserved | Option RAM bank 6 failure | Defined in ROM |
| `0026` | System Error / reserved | Option RAM bank 7 failure | Defined in ROM |
| `0028` | System Error | Option ROM at `F4000h` failed CRC | Option-ROM test |
| `0029` | System Error | Option ROM at `F6000h` failed CRC | Option-ROM test |
| `002A` | System Error | Winchester option ROM at `F8000h` failed | Handbook |
| `002B` | System Error | Option ROM at `FA000h` failed CRC | Option-ROM test |
| `002C` | System Error | ROM at `FC000h` failed | Handbook |
| `002D` | System Error | Main System ROM at `FE000h` failed | Defined in ROM source |
| `0030` | System Error | No diskette drives installed | |
| `0031` | System Error | System could not boot from any drive | |
| `0032` | System Error | CRC diskette read error | Read error, hardware CRC failed  |
| `0033` | System Error | Diskette seek error | Track not found |
| `0034` | System Error | Sector not found | |
| `0035` | System Error | FDC interface / controller failure | |
| `0036` | System Error | Not a system diskette | Boot sector ID missing |
| `0037` | System Error | Diskette format error / no data received | |
| `0038` | System Error | Boot-sector CRC error or bad sector buffer | TI specific boot sector CRC failed | 
| `0039` | System Error | DRQ error from controller | |
| `1040` | System Error | Unexpected NMI during operation | Runtime error |
| `1041` | System Error | RAM parity error during operation | Runtime error |
| `1042` | System Error | Unexpected software or hardware interrupt | Interrupt catcher / soft lock |
| `1050` | System Error | Fatal software condition encountered | Runtime error |

The Maintenance Handbook documents the on-screen codes `0001`–`0007` in **Table B-1** and the three system LEDs separately in **Table B-3**. They belong to the same POST sequence but must not be treated as identical representations.

---

### 2 LED Binary Values During POST
See ROM `ROMERR` module. The original source also notes that the LED bits are inverted because the LEDs are active-low.  
The LEDs are located along the outer edge of the systemboard and are visible from the outside.

| LED binary value | Phase | Notes |
|---:|---|---|
| `7` | Hardware Reset | POST start / all LEDs on |
| `6` | ROM | ROM-test phase |
| `5` | RAM | Base-RAM test phase |
| `4` | Interrupt / timer / early hardware tests | Timing problems may surface here |
| `3` | FDC | Floppy-controller test block |
| `2` | CRT | CRT/video test block |
| `1` | Option / Winchester | Optional hardware phase |
| `0` | POST complete | Normal completion / LEDs off |


The LED display marks the current or last-reached POST state, while the on-screen error code identifies the error class. For `System Error 0004`, the ROM logic seems more detailed than the handbook wording: the same stage covers the interrupt-controller tests and the timer-accuracy tests.

---

### 3. Parallel port test-plug subcodes 

Since I don't own a parallel-port test plug, I haven't been able to verify this information.

#### 3.1 Interrupt / Timer

| Bit | Meaning |
|---:|---|
| `01h` | Invalid interrupt |
| `02h` | NMI interrupt failure |
| `04h` | Timer interrupt failure |
| `08h` | FDC interrupt failure |
| `10h` | Keyboard interrupt failure |
| `20h` | Timer 0 failure |
| `40h` | Timer 1 failure |
| `80h` | Timer 2 failure |

These values are bit masks and may occur in combination.


#### 3.2. RAM

| Test-plug display | Meaning |
|---|---|
| `00000000` | General RAM/parity failure |
| `XXXXXXX1` | LSB failure |
| `1XXXXXXX` | MSB failure |
| `11111111` | All data bits failed |


#### 3.3. FDC

| Pattern / bit | Meaning |
|---|---|
| `xxxxxxx1` | FDC sector-buffer even-bit failure |
| `xxxxxx1x` | FDC sector-buffer odd-bit failure |
| `xxxxx1xx` | Controller register test failure |
| `xxxx1xxx` | Restore/command-execution failure |


#### 3.4 CRT

| Bit | Meaning |
|---:|---|
| `01h` | CRT attribute memory failure |
| `02h` | CRT attribute latch failure |
| `04h` | CRT controller/register failure |
| `08h` | CRT character memory failure |
| `10h` | CRT video output failure |
| `20h` | CRT interrupt / reserved subtest depending on listing revision |
| `40h` | Vertical-blank / CRT timing-related failure |
| `80h` | Reserved for future option |

---

## 4. Sources

- Texas Instruments Professional Computer Maintenance Handbook
- TI System ROM Listing V1.23
- System ROM dumps of versions 1.23, 1.24, and 1.26
- MAME/TIPC POST analysis
