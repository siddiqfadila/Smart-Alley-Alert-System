# Smart-Alley-Alert-System
Smart Alley Alert System adalah sistem peringatan otomatis berbasis computer vision yang mendeteksi pergerakan dari dalam gang menuju jalan raya. Sistem ini memberi peringatan visual atau audio kepada pengendara untuk mencegah tabrakan dan kemacetan, serta mendukung pemantauan real-time berbasis web.

📋 Table of Contents

- Overview
- Problem Statement
- Solution
- Tech Stack
- System Architecture
- Features
- Hardware Requirements
- Installation
- Usage
- Demo
- Project Scope & Limitations
- Future Improvements
- Contributing
- License
- Contact


🎯 Overview
This project is a proof-of-concept security system designed for narrow alley (gang) monitoring. It uses computer vision with background subtraction algorithm to detect suspicious movements using a dual ROI (Region of Interest) approach.
Project Type: Final Project (Tugas Akhir) - D3 Computer Engineering
Status: Functional Prototype
Development Period: [Your timeframe, e.g., August - December 2024]

🚧 Problem Statement
Narrow alleys in residential areas are vulnerable to crime due to:

Limited visibility from main roads
Difficulty in installing traditional CCTV monitoring
Lack of real-time alert systems for residents
High cost of commercial security systems


💡 Solution
A low-cost, automated motion detection system that:

Monitors specific direction of movement using dual ROI zones
Triggers alerts (buzzer + 12V LED lamp) only for suspicious patterns
Provides live video streaming via web dashboard
Logs all detection events for review
Runs autonomously on boot (systemd + rc.local)

Gang Layout (Top View):
Main Road ──────────────────
              │
              │ ROI 2 (Confirm Zone)
              │
              │ ROI 1 (Entry Zone)
              │
           [Inside Alley]

Logic:
✅ ROI 2 → ROI 1: Normal (exit from alley) → No alarm
⚠️ ROI 1 → ROI 2: Suspicious (entering alley) → Trigger alarm
This directional detection reduces false positives from residents exiting the alley while catching potential intruders entering.

🛠️ Tech Stack
Hardware

Raspberry Pi 3 Model B+
Pi Camera Module v2 (8MP, 1080p)
12V Relay Module (for LED lamp control)
Active Buzzer (5V)
12V LED Lamp (warning indicator)
Power Supply (5V 2.5A for RPi + 12V for lamp)

Software

Python 3.7+
OpenCV 4.x - Computer vision processing
Flask 2.x - Web interface
Flask-SocketIO - Real-time communication
picamera2 - Camera interface
RPi.GPIO - Hardware control
Background Subtraction (MOG2) - Motion detection algorithm

Why Background Subtraction instead of ML/DL?

Resource Efficiency: Runs smoothly on RPi 3B+ (~1.2 GHz CPU)
Real-time Performance: <500ms response time
Low Power Consumption: ~5W total system power
Sufficient Accuracy: 90%+ detection rate in controlled environment
No Training Data Required: Works out-of-the-box


🏗️ System Architecture
┌─────────────────────────────────────────────────────┐
│              Raspberry Pi 3B+                       │
│                                                     │
│  ┌──────────────┐         ┌──────────────┐          │
│  │ Pi Camera v2 │───────▶│ motion_      │          │
│  │   (1080p)    │         │ detector.py  │          │
│  └──────────────┘         │              │          │
│                           │ • Background │          │
│                           │   Subtraction│          │
│                           │ • Dual ROI   │          │
│                           │ • Direction  │          │
│                           │   Logic      │          │
│                           └───────┬──────┘          │
│                                   │                 │
│                           Socket (Port 8888)        │
│                                   │                 │
│                           ┌───────▼──────┐          │
│                           │   app.py     │          │
│                           │              │          │
│                           │ • Flask Web  │          │
│                           │ • Video      │          │
│                           │   Streaming  │          │
│                           │ • SocketIO   │          │
│                           └───────┬──────┘          │
│                                   │                 │
│                               Port 5000             │
└───────────────────────────────────┼───────────────┘
                                    │
                            ┌───────▼──────┐
                            │   Browser    │
                            │              │
                            │ • Live Feed  │
                            │ • Dashboard  │
                            │ • Status     │
                            └──────────────┘

Hardware Output:
├─ GPIO 17 ──▶ Buzzer (Alert Sound)
└─ GPIO 27 ──▶ Relay ──▶ 12V LED Lamp (Visual Alert)

✨ Features
Core Functionality

✅ Dual ROI Motion Detection with directional logic
✅ Real-time Video Streaming (MJPEG over HTTP)
✅ Automated Alarm System (buzzer + LED lamp)
✅ Sequence Counting (tracks consecutive detections)
✅ Alarm Cooldown (5s to prevent spam)
✅ Live Dashboard with system status
✅ Event Logging (file-based with timestamps)

System Features

✅ Auto-start on Boot (systemd + rc.local)
✅ Graceful Shutdown (signal handling)
✅ Thread-safe Operations (concurrent camera & API)
✅ Remote Shutdown (via web interface)
✅ Configurable Parameters (centralized config)


🔧 Hardware Requirements
ComponentSpecificationQuantityRaspberry Pi3 Model B+ (1GB RAM)1Pi Camerav2 (8MP, 1080p)1Relay Module5V, 1-channel1BuzzerActive, 5V1LED Lamp12V, 3-5W1Power Supply5V 2.5A (RPi)1Power Supply12V (LED)1Jumper WiresMale-to-Female~10BreadboardHalf-size (optional)1
GPIO Pin Mapping
pythonBUZZER_PIN = 17  # BCM mode
LAMP_PIN = 27    # BCM mode

📦 Installation
Prerequisites

Raspberry Pi OS (Bullseye or newer)
Python 3.7+
Pi Camera enabled in raspi-config

Step-by-Step Setup

Clone the Repository

bashgit clone https://github.com/yourusername/gang-motion-detection.git
cd gang-motion-detection

Create Virtual Environment

bashpython3 -m venv venv
source venv/bin/activate

Install Dependencies

bashpip install -r requirements.txt

Configure System

bash# Edit config.py to adjust ROI coordinates, thresholds, etc.
nano src/config.py

Test Run

bashcd src
python motion_detector.py  # Test detector only
python app.py              # Test web interface

Setup Auto-start (Optional)

bash# Copy systemd service
sudo cp deployment/systemd/motion-detector.service /etc/systemd/system/
sudo systemctl enable motion-detector.service
sudo systemctl start motion-detector.service

# Check status
sudo systemctl status motion-detector.service
For detailed installation guide, see docs/INSTALLATION.md

🚀 Usage
Manual Run
bashcd src
python app.py
Access dashboard at: http://<raspberry-pi-ip>:5000
System Service (After installation)
bashsudo systemctl start motion-detector
sudo systemctl stop motion-detector
sudo systemctl restart motion-detector
Web Dashboard Features

Live Video Feed: Real-time camera stream with ROI overlay
System Status: API connection, sequence count, last detection time
Alert Log: Recent detections displayed dynamically
Shutdown Button: Safe system shutdown


🎬 Demo
Video Demo
▶️ Watch Full Demo on YouTube
Screenshots
Dashboard ViewROI DetectionAlert TriggeredShow ImageShow ImageShow Image

⚠️ Project Scope & Limitations
This is a PROTOTYPE/PROOF-OF-CONCEPT
This project was developed as a final assignment for D3 Computer Engineering, focused on system design and functionality rather than production deployment.
Current Status
✅ Working Features:

Motion detection algorithm
Dual ROI directional logic
Web streaming interface
Hardware control (buzzer + lamp)
Auto-start system

⚠️ Known Limitations:

Not production-hardened: No weatherproofing or outdoor enclosure
Requires continuous power: No battery backup
Local network only: No remote internet access
Indoor testing only: Not tested in various weather conditions
No persistence: Detection logs reset on reboot
Limited to 1 camera: No multi-camera support

Test Environment

Location: Indoor corridor (simulating alley)
Lighting: Constant artificial lighting
Runtime tested: Up to 6 hours continuous operation
Detection accuracy: ~90% in controlled conditions


🔮 Future Improvements
Short-term Enhancements

 Add weather-resistant enclosure design
 Implement battery backup system (UPS)
 Add persistent database (SQLite) for detection logs
 Create mobile-responsive dashboard
 Add configuration UI (no need to edit config.py)

Long-term Roadmap

 Cloud integration: MQTT broker for remote monitoring
 Mobile notifications: Telegram/WhatsApp bot integration
 ML/DL upgrade: YOLO for person classification (reduce false positives)
 Multi-camera support: Cover longer alleys
 Night vision: IR camera module
 Two-way audio: Speaker for warnings
 Edge AI acceleration: Coral USB Accelerator or Intel NCS2


📊 Performance Metrics
MetricValueFrame Processing~15-20 FPS (640x480)Detection Latency<500msPower Consumption~5W (system) + 3W (lamp)System Boot Time~45 secondsDetection Accuracy~90% (controlled environment)False Positive Rate~5% (with proper ROI tuning)

🤝 Contributing
While this is primarily an academic project, feedback and suggestions are welcome!

Fork the repository
Create your feature branch (git checkout -b feature/AmazingFeature)
Commit your changes (git commit -m 'Add some AmazingFeature')
Push to the branch (git push origin feature/AmazingFeature)
Open a Pull Request


📄 License
This project is licensed under the MIT License - see the LICENSE file for details.

👤 Contact
Siddiq Fadila
D3 Computer Engineering - [Politeknik Pajajaran ICB]
Final Project (2025)

📧 Email: siddiqfadila.official.com
💼 LinkedIn: linkedin.com/in/Muhammad Siddiq Fadila Ibrohim
🐙 GitHub: @siddiqfadila
📱 Portfolio: yourportfolio.com


🙏 Acknowledgments

Advisor: [Advisor Name] - Project guidance
Institution: [Politeknik Pajajaran ICB]
OpenCV Community - Computer vision resources
Raspberry Pi Foundation - Hardware platform


<div align="center">
⭐ If you find this project interesting, please consider giving it a star!
Made with ❤️ for community safety
</div>
