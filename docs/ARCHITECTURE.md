# System Architecture

This document describes the technical architecture of PROJECT CYBERPUNK, including hardware components, software stack, and functional workflows.

---

## 1. Hardware Stack

### 1.1 Primary Computing Device

- **Android smartphone** (Samsung Galaxy S7 or newer)
- **Kali NetHunter** installed (rooted)
- **Minimum specifications**: 4GB RAM, 64GB Storage
- **USB with OTG support**

### 1.2 Audio/Visual Interface

- **Meta Ray-Ban Smartglasses** (or comparable)
- **Integrated camera** (12MP, video)
- **Bluetooth 5.0+ Audio** (microphone + speakers)
- **Optional: HUD display** (for AR-capable models)

Note: Meta Ray-Ban do NOT have a HUD. For HUD functionality, more expensive AR glasses are required (~800-1500 EUR).

### 1.3 Wireless Penetration Tools

- **USB WiFi Adapter** (Alfa AWUS036ACH or similar)
- **Monitor Mode + Packet Injection Support**
- **Dual-Band** (2.4 + 5 GHz)
- **USB OTG adapter**

### 1.4 Additional Sensors

- **USB SDR dongle** (RTL-SDR) for RF/EMI monitoring
- **External GPS antenna** (if smartphone GPS is insufficient)
- **Powerbank** (20,000+ mAh) for extended operations

---

## 2. Software Stack

### 2.1 Operating System Layer
```
Kali NetHunter (Android)
├── Termux (Terminal Emulator)
├── NetHunter App (GUI)
└── Android System Services
```

### 2.2 Intelligence Layer
```
ShellGPT
├── OpenAI API / Local LLM
├── Command Generation
├── Context Management
└── Response Formatting
```

### 2.3 Voice Processing Pipeline
```
STT Engine (Whisper/Vosk)
├── Wake Word Detection
├── Intent Classification
├── Command Parsing
└── TTS Engine (espeak-ng/Android TTS)
```

### 2.4 Specialized Tools

#### Penetration Testing
```
├── aircrack-ng suite
├── Reaver/Pixiewps
├── Kismet
├── nmap/masscan
└── Metasploit Framework
```

#### OSINT & Geolocation
```
├── Maltego (via remote connection to desktop instance)
├── OR: Mobile OSINT alternatives (Hunchly Mobile, etc.)
├── Shodan CLI
├── theHarvester
├── Exiftool
└── Custom GPS Tagging Scripts
```

#### RF/EMI Analysis
```
├── GQRX
├── rtl_433
├── Universal Radio Hacker
└── Spectrum Analyzer Tools
```

---

## 3. Functional Architecture

### 3.1 Voice Command Processing Flow
```
[Smartglasses Mic]
        ↓ Bluetooth Audio
[Android Audio Input]
        ↓
[Wake Word Detector] → "Operator" / "Kali" / "System"
        ↓
[Speech-to-Text Engine]
        ↓
[Command Dispatcher]
        ├→ Direct Shell Command
        ├→ ShellGPT Query
        ├→ OSINT Request
        ├→ Media Capture
        └→ System Control
        ↓
[Execution Engine]
        ↓
[Response Formatter]
        ↓
[Text-to-Speech]
        ↓ Bluetooth Audio
[Smartglasses Speaker]
```

### 3.2 Natural Language Command Translation

#### Translation Layer

The system uses a hybrid approach combining predefined mappings with AI-powered interpretation for complex queries.

**Example Command Mappings:**
```python
COMMAND_MAPPINGS = {
    "scan the network": "nmap -sn {target_network}",
    "find access points": "airodump-ng {interface}",
    "check for vulnerabilities": "nmap -sV --script vuln {target}",
    "analyze this signal": "rtl_sdr -f {frequency} -",
    "who owns this network": "whois {domain}",
    "take a picture": "capture_photo --gps --timestamp",
    "record this": "start_recording --duration {seconds}",
    "show me the data": "display_on_hud {data_type}"
}
```

#### Context-Aware Processing

The system maintains contextual awareness through:

- **Recognition of targets from previous context**
- **GPS-based auto-completion** of network ranges
- **History of recent commands** for quick reference
- **Variable management** for frequent targets

### 3.3 Multimedia Intelligence Gathering

#### Automated Geotagging

Every capture is automatically enriched with contextual metadata:
```python
class IntelCapture:
    def capture_with_context(self):
        metadata = {
            'timestamp': get_utc_time(),
            'gps': get_precise_location(),
            'wifi_networks': scan_nearby_ssids(),
            'bluetooth_devices': scan_bt_devices(),
            'cell_towers': get_cell_info(),
            'rf_signature': capture_rf_spectrum(),
            'audio_ambient': record_ambient(duration=5)
        }
        return media + metadata
```

#### Capture Modes

The system supports multiple capture modes for different operational scenarios:

- **Silent Photo** - No shutter sound for discreet capture
- **Continuous Video Recording** - Extended surveillance
- **Audio Logging** - Ambient sounds and voice commands
- **RF Spectrum Snapshot** - Radio frequency environment capture
- **Combined Intelligence Package** - All modalities synchronized

---

## 4. Data Flow Architecture

### 4.1 Information Flow
```
[Physical Environment]
        ↓
[Multi-Modal Sensors]
├── Camera (Visual)
├── WiFi Adapter (Network)
├── GPS (Location)
├── SDR (RF Spectrum)
└── Microphone (Audio)
        ↓
[Data Collection Layer]
        ↓
[Metadata Enrichment]
        ↓
[Secure Storage]
└── Encrypted Container
```

### 4.2 Command Execution Flow
```
[Voice Command]
        ↓
[Natural Language Processing]
        ↓
[Intent Classification]
        ├→ [Predefined Pattern Match] (Fast Path: <100ms)
        └→ [ShellGPT Interpretation] (Complex Queries: 1-3s)
        ↓
[Command Validation]
        ↓
[Execution]
        ↓
[Result Formatting]
        ↓
[Voice Feedback]
```

---

## 5. System Integration Points

### 5.1 Hardware Integration

- **Bluetooth** - Audio I/O with smartglasses
- **USB OTG** - WiFi adapter and SDR connection
- **WiFi** - Internal for LTE/data, external for pentesting
- **GPS** - Precise geolocation for all captures

### 5.2 Software Integration

- **Android Services** - System-level access for NetHunter
- **Termux** - Linux environment for tools
- **ShellGPT API** - Natural language interpretation
- **Cloud Services** (optional) - OSINT queries, LLM access

---

## 6. Security & OPSEC Architecture

### 6.1 Data Protection

- **Encrypted storage** with plausible deniability (VeraCrypt hidden volumes)
- **Secure communication** channels (VPN/Tor for exfiltration)
- **Emergency wipe** capability via panic button
- **No cloud storage** of sensitive reconnaissance data

### 6.2 Operational Security

- **Bluetooth MAC randomization** to prevent tracking
- **Minimal TX power** for reduced detection radius
- **No visible screen activity** during operations
- **Natural behavior patterns** (phone conversation camouflage)

---

_Last updated: January 2025_
