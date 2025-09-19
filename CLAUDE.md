# SunFounder PiCar-X Robot Car Setup with Raspberry Pi 5 (16GB RAM)

## 📋 Quick Status (Updated September 19, 2025)
**Current State**: All SunFounder modules installed on Pi 5, ready for I2C setup and calibration  
**Repository Organization**: All SunFounder repos cloned locally in `/home/arestlel/repos/`  
**Key Lesson**: SSH >> SunFounder Create Agent web UI (much faster/better terminal experience)  
**Connection**: `ssh pi@<PI_IP>` from Fedora laptop ✅ Working

## 📁 Repository Structure
```
/home/arestlel/repos/
├── robotcar/           # Your project documentation
│   └── CLAUDE.md      # This file - setup guide & progress
├── picar-x/           # Main PiCar-X repository 
│   ├── example/       # Example scripts (movement, vision, etc.)
│   ├── picarx/        # Core PiCar-X Python module
│   └── gpt_examples/  # GPT-4 integration examples
├── robot-hat/         # Hardware abstraction layer
│   └── robot_hat/     # GPIO, servo, motor control
└── vilib/             # Computer vision library
    ├── examples/      # Vision examples (color detect, face detect)
    └── vilib/         # Core vision processing
```

## Overview
This guide provides comprehensive instructions for setting up a SunFounder PiCar-X Robot Car with a Raspberry Pi 5 (16GB RAM). The PiCar-X is officially compatible with the Pi 5 and offers advanced robotics capabilities including computer vision, obstacle avoidance, line tracking, and more.

## Hardware Requirements
- SunFounder PiCar-X Kit
- Raspberry Pi 5 (16GB RAM)
- MicroSD card (32GB+ recommended)
- Reliable power supply for Pi 5
- WiFi network access

## Step 1: Assembly
Follow the [PiCar-X Assembly Guide](https://docs.sunfounder.com/projects/picar-x-v20/en/latest/) to construct the mechanical components:
- Chassis assembly with front/rear wheels
- Motor mounting
- Battery holder installation
- Ultrasonic sensor mounting
- Camera module installation
- Raspberry Pi 5 mounting

## Step 2: Raspberry Pi OS Installation

### Download and Flash OS
1. Use the official Raspberry Pi Imager
2. Select Raspberry Pi OS (64-bit recommended for Pi 5)
3. Flash to microSD card

### Enable Headless Setup (Optional)
For headless operation without monitor:
1. Create empty `ssh` file in `/boot/` directory
2. Create `wpa_supplicant.conf` file in `/boot/` with WiFi credentials:
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="Your_SSID"
    psk="Your_Password"
    key_mgmt=WPA-PSK
}
```

## Step 3: Initial Pi 5 Setup

### Boot and Connect
1. Insert microSD card into Pi 5
2. Connect power supply
3. Wait for boot process
4. Connect via SSH if headless: `ssh pi@<PI_IP_ADDRESS>`

### Better Connection Methods from Linux Laptop (Fedora/MacBook/etc.)

#### Option 1: SSH (Recommended for Terminal Commands) ✅ CURRENT SETUP
**Much better than SunFounder Create Agent web interface!**
```bash
# From your Linux laptop terminal
ssh pi@<PI_IP_ADDRESS>

# Example:
ssh pi@192.168.1.100
```
**Status**: ✅ Connected and working from Fedora 42 laptop

#### Option 2: VS Code Remote SSH (Best for Development)
1. Install "Remote - SSH" extension in VS Code
2. Press `Ctrl+Shift+P` → "Remote-SSH: Connect to Host" 
3. Enter: `pi@<PI_IP_ADDRESS>`
4. Full IDE experience with file editing, terminal, and debugging

#### Option 3: VNC for GUI Access
```bash
# On Pi 5, enable VNC
sudo raspi-config
# Interface Options → VNC → Enable

# From laptop, use VNC Viewer app
# Connect to: <PI_IP_ADDRESS>:5900
```

### Finding Your Pi's IP Address
```bash
# On Pi 5 (if you have access)
hostname -I

# From Linux laptop, scan network
nmap -sn 192.168.1.0/24

# Or check router's admin page
```

### Update System
```bash
sudo apt update  # ✅ STARTED (in progress via SSH)
sudo apt upgrade
```

### Install Python Dependencies
**Important**: For Lite OS versions, install Python3 packages:
```bash
sudo apt install git python3-pip python3-setuptools python3-smbus
```

## 🚀 CURRENT STATUS (Ready for Final Setup!)

### What's Done ✅
- ✅ Pi 5 hardware assembled and powered
- ✅ Raspberry Pi OS installed and booted  
- ✅ SSH connection established from Fedora 42 laptop
- ✅ System update completed (`sudo apt update && upgrade`)
- ✅ All PiCar-X modules installed (robot-hat, vilib, picar-x v2.0.5)
- ✅ I2S audio configured and tested successfully
- ✅ **Repository organization completed** - All SunFounder repos cloned locally
- ✅ Ready for reboot to activate all changes

### Next Steps (Final Setup!) 🎯
1. **Reboot Pi 5** (required for I2S audio changes):
   ```bash
   sudo reboot
   ```

2. **After reboot - SSH back in and enable I2C**:
   ```bash
   ssh pi@<PI_IP>
   sudo raspi-config
   # Navigate: Interface Options → I2C → Enable → Finish
   ```

3. **Calibrate servos for accurate movement**:
   ```bash
   cd ~/picar-x/example/calibration
   python3 calibration.py
   # Follow on-screen instructions to calibrate steering and camera servos
   ```

4. **Test the robot car**:
   ```bash
   # Start the web server
   cd ~/picar-x/example
   python3 11.video_car.py
   # Access control interface: http://<PI_IP>:8000
   ```

### Connection Info 📡
- **Current setup**: SSH from Fedora 42 laptop to Pi 5
- **Connection**: `ssh pi@<PI_IP_ADDRESS>` 
- **Status**: Working great, much better than SunFounder Create Agent web UI

## Step 4: Install PiCar-X Software Stack

### Install Robot HAT Module
```bash
cd ~/
git clone -b v2.0 https://github.com/sunfounder/robot-hat.git
cd robot-hat
sudo python3 setup.py install
```

### Install Vision Library (vilib)
```bash
cd ~/
git clone -b picamera2 https://github.com/sunfounder/vilib.git
cd vilib
sudo python3 install.py
```

### Install PiCar-X Module
```bash
cd ~/
git clone -b v2.0 https://github.com/sunfounder/picar-x.git --depth 1
cd picar-x
sudo python3 setup.py install
```
*Note: This step may take several minutes to complete.*

### Install I2S Audio Components
```bash
cd ~/picar-x
sudo bash i2samp.sh
```
Follow the prompts:
- Type `y` and press Enter to continue
- Type `y` and press Enter to run `/dev/zero` in background
- Type `y` and press Enter to restart the PiCar-X

*Note: If audio doesn't work after restart, run `i2samp.sh` script multiple times.*

## Step 5: Enable I2C Interface
```bash
sudo raspi-config
```
Navigate to `Interface Options` > `I2C` > Enable

## Step 6: Servo Calibration
Before first use, calibrate the servos for accurate movement:
```bash
cd ~/picar-x/examples
python3 calibrate.py
```

## Step 7: Start the Web Server
```bash
cd ~/picar-x/examples/remote_control
sudo python3 start_server.py
```

Access the control interface at: `http://<PI_IP_ADDRESS>:8000`

## Pi 5 Specific Optimizations

### Memory Configuration
With 16GB RAM, you can optimize for computer vision tasks:
```bash
sudo nano /boot/config.txt
```
Add:
```
gpu_mem=256
```

### Performance Settings
For maximum performance, consider:
- Using performance governor: `sudo cpufreq-set -g performance`
- Increasing swap file size for intensive AI tasks
- Using faster microSD card (Class 10 or higher)

## Available Features

### Basic Controls
- Movement control (forward, backward, left, right)
- Speed adjustment
- Camera pan/tilt control

### Advanced Features
- **Obstacle Avoidance**: Uses ultrasonic sensor
- **Line Tracking**: Follows lines using camera vision
- **Cliff Detection**: Prevents falls from edges
- **Computer Vision**: Object detection and recognition
- **Text-to-Speech**: Audio feedback
- **Video Recording**: Capture and stream video
- **APP Control**: Mobile app integration

## Programming Examples

### Basic Movement
```python
import picarx
from time import sleep

car = picarx.Picarx()

# Move forward
car.forward(50)  # Speed 0-100
sleep(2)

# Turn right
car.set_dir_servo_angle(30)  # Steering angle
sleep(1)

# Stop
car.stop()
```

### Computer Vision Setup
```python
from vilib import Vilib
from picarx import Picarx

car = Picarx()
Vilib.camera_start(vflip=False, hflip=False)
Vilib.display(local=True, web=True)

# Enable object detection
Vilib.color_detect_switch(True)
Vilib.color_detect("red")  # Detect red objects
```

## Troubleshooting

### Connection Issues
1. **SunFounder Create Agent Web UI Problems**: ⚠️ CONFIRMED ISSUE
   - Web interfaces are slow/clunky for terminal commands
   - **Solution**: ✅ SOLVED - Use SSH from Linux laptop terminal instead
   - **Result**: Much faster and more reliable for installation commands
   - **Lesson**: Always use SSH for command-line work, web UI only for robot control later
   
2. **Cannot SSH to Pi 5**:
   - Ensure SSH is enabled (add empty `ssh` file to `/boot/`)
   - Check IP address: `nmap -sn 192.168.1.0/24` from laptop
   - Try default credentials: username `pi`, password `raspberry`

3. **Connection Timeouts**:
   - Check WiFi credentials in `wpa_supplicant.conf`
   - Ensure Pi 5 and laptop on same network
   - Router firewall may be blocking connections

### Common Issues
1. **No Sound**: Re-run `i2samp.sh` script multiple times
2. **Camera Not Working**: Check camera cable connections
3. **Servo Calibration**: Run calibration script if movement is inaccurate
4. **Network Issues**: Verify WiFi credentials in `wpa_supplicant.conf`

### Pi 5 Specific
- Ensure all software is updated for Pi 5 compatibility
- Use official Pi 5 power supply for stable operation
- Check for latest firmware updates

### Linux Laptop Commands for Pi 5 Management (Fedora/Ubuntu/etc.)
```bash
# Connect via SSH (current working setup)
ssh pi@<PI_IP>

# Copy files to Pi 5
scp file.py pi@<PI_IP>:/home/pi/

# Run commands remotely
ssh pi@<PI_IP> "sudo apt update"

# Mount Pi 5 filesystem (with sshfs)
# Fedora: sudo dnf install fuse-sshfs
# Ubuntu: sudo apt install sshfs
sshfs pi@<PI_IP>:/home/pi ~/pi5-mount
```

## Resources
- [Official PiCar-X Documentation](https://docs.sunfounder.com/projects/picar-x-v20/en/latest/)
- [SunFounder Community Forum](https://forum.sunfounder.com/)
- [GitHub Repository](https://github.com/sunfounder/picar-x)
- [Video Tutorials](https://www.sunfounder.com/products/picar-x)

## Next Steps

### Immediate Actions (Fedora Laptop → Pi 5) ✅ DONE
1. **✅ Successfully switched from web interface to SSH**:
   ```bash
   # From Fedora laptop terminal - confirmed much better than web UI!
   ssh pi@<PI_IP_ADDRESS>
   
   # ✅ Started system update (in progress)
   sudo apt update
   # Next: sudo apt upgrade
   ```

2. **Future development environment options**:
   - Install VS Code Remote SSH extension
   - Connect directly to Pi 5 for coding/debugging

### Long-term Development
1. Complete servo calibration
2. Test basic movement controls  
3. Explore computer vision examples
4. Develop custom applications
5. Integrate with AI frameworks for advanced robotics

### Why SSH is Better Than Web UI
✅ **Faster command execution**  
✅ **Better terminal experience**  
✅ **Copy/paste works properly**  
✅ **No browser lag/refresh issues**  
✅ **Can run multiple terminal sessions**  
✅ **Full keyboard shortcuts support**

---
*Last Updated: September 19, 2025*
*Compatible with: Raspberry Pi 5, PiCar-X v2.0*
