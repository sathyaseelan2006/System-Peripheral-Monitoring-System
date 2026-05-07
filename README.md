# DeskBot

> An ESP32 power-monitoring and alert system with a mobile client and a Node.js backend.

DeskBot is built around a simple goal: watch a connected device, detect power changes or disconnects, and surface alerts in a way that is easy to operate day to day. The repository currently includes an ESP32-focused setup, a mobile app workspace, and a server-side notification stack.

## What’s inside

| Part | Purpose |
| --- | --- |
| ESP32 firmware | Reads device power-related data and sends monitoring updates |
| Mobile app | Flutter-based mobile workspace for local monitoring and control |
| Server | Node.js service for alert delivery and persistence |
| Notifications | Email and WhatsApp configuration via the server environment template |

## At a glance

- Real-time monitoring for connected devices
- Alerting for power drops or device disconnects
- Mobile and server-based deployment paths
- MongoDB-backed server storage
- Email and WhatsApp integration options

## Repository layout

```text
README.md
DEMO_Proof/
mobile-app/
server/
src/
```

The workspace also contains Android build files under `mobile-app/android/` and a server environment template at `server/.env.example`.

## Hardware notes

The project is documented around an ESP32 setup with an INA219 sensor, jumper wires, and a USB-powered device under test such as a mouse.

Typical wiring shown in the existing docs:

```text
INA219 -> ESP32
VCC    -> 3.3V
GND    -> GND
SDA    -> GPIO 21
SCL    -> GPIO 22
```

## Server configuration

The checked-in environment template uses these defaults:

- `PORT=3001`
- `NODE_ENV=development`
- `MONGODB_URI=mongodb://localhost:27017/esp32_monitor`
- Gmail-based email alerts
- Twilio-based WhatsApp alerts
- Independent toggles for email and WhatsApp notifications

See `server/.env.example` for the full list of variables.

## Mobile app and server paths

This repository supports two operating styles:

1. A mobile app workflow for local monitoring on a phone
2. A traditional server workflow with MongoDB and external notifications

Both paths are represented in the repository so you can work with the setup that matches your environment.

## Good to know

- The docs in this repository are focused on practical setup rather than a polished product release.
- Some parts of the workspace are platform-specific build artifacts, especially under `mobile-app/android/`.
- Configuration values should be filled in with your own device, network, and notification details.

## Acknowledgment

DeskBot is a hands-on IoT project repository, so the README keeps the focus on what is actually present in the codebase: ESP32 monitoring, a mobile app workspace, a Node.js server, and the alerting configuration around them.

### DELETE /api/alerts/cleanup
Delete old alerts (default: older than 30 days)

## 🧪 Testing the System

### 1. Test Server

```powershell
# Test if server is running
curl http://localhost:3000

# View recent alerts
curl http://localhost:3000/api/alerts

# View all devices
curl http://localhost:3000/api/devices
```

### 2. Test ESP32

1. Open Serial Monitor in PlatformIO
2. Watch for WiFi connection and sensor readings
3. **Unplug the mouse** to trigger an alert
4. Check serial output for "Alert sent to server successfully!"
5. Check your email for the notification

### 3. Test Email Manually

```javascript
// server/test-email.js
require('dotenv').config();
const emailService = require('./services/emailService');

const testAlert = {
  deviceId: 'TEST_DEVICE',
  location: 'Test Location',
  alertType: 'CRITICAL',
  voltage: 3.85,
  current: 45.2,
  power: 174.1,
  receivedAt: new Date()
};

emailService.sendAlertEmail(testAlert)
  .then(() => console.log('Test email sent!'))
  .catch(err => console.error('Error:', err));
```

```powershell
node test-email.js
```

## 🔍 Troubleshooting

### ESP32 Won't Connect to WiFi
- Check SSID and password are correct
- Ensure ESP32 is in range of WiFi
- Check if WiFi is 2.4GHz (ESP32 doesn't support 5GHz)

### Server Connection Failed
- Verify server is running (`curl http://localhost:3000`)
- Check firewall settings
- Ensure ESP32 and server are on same network
- Use correct IP address in ESP32 code

### Email Not Sending
- Check Gmail app password (not regular password)
- Verify 2-factor authentication is enabled
- Check spam/junk folder
- Review server console for error messages

### MongoDB Connection Error
- Ensure MongoDB is running
- Check MONGODB_URI in .env file
- For cloud: verify network access in MongoDB Atlas

### INA219 Not Found
- Check I2C connections (SDA, SCL)
- Verify 3.3V power supply
- Run I2C scanner (included in code)
- Check sensor address (default: 0x40)

## 📱 Monitoring Dashboard (Future Enhancement)

The API is ready for a web dashboard. You can build a frontend using:
- React/Vue.js for web interface
- Chart.js for data visualization
- Real-time updates with Socket.io

## 🔒 Security Recommendations

1. **Don't commit .env file** (already in .gitignore)
2. Use HTTPS in production
3. Add authentication to API endpoints
4. Restrict MongoDB access
5. Use environment-specific configurations

## 📝 Log Files

Server logs are output to console. For production:

```javascript
// Add to server.js
const winston = require('winston');
const logger = winston.createLogger({
  transports: [
    new winston.transports.File({ filename: 'error.log', level: 'error' }),
    new winston.transports.File({ filename: 'combined.log' })
  ]
});
```

## 📚 Documentation

### Mobile App Documentation
- **[Mobile App Setup Guide](MOBILE-APP-SETUP.md)** - Complete setup instructions for the mobile app
- **[Quick Start Guide](mobile-app/QUICKSTART.md)** - Get started in 5 minutes
- **[ESP32 Mobile Configuration](ESP32-MOBILE-CONFIG.md)** - Configure ESP32 for mobile app
- **[Mobile App README](mobile-app/README.md)** - Detailed mobile app documentation

### General Documentation
- **[Architecture Overview](ARCHITECTURE.md)** - System architecture and data flow
- **[Project Summary](PROJECT-SUMMARY.md)** - Complete project overview
- **[Gmail Setup](GMAIL-SETUP.md)** - Email notification setup (Node.js server)
- **[WhatsApp Setup](WHATSAPP-SETUP.md)** - WhatsApp notification setup (Node.js server)
- **[MongoDB Setup](MONGODB-SETUP.md)** - Database setup (Node.js server)

## 🆘 Support

For issues or questions:
1. Check troubleshooting section above
2. Review serial monitor output from ESP32
3. Check server logs for errors
4. Verify network connectivity

## 📄 License

MIT License - feel free to modify and use for your projects!

## 🎉 Credits

### Mobile App Built with:
- Flutter & Dart
- Hive (Local Database)
- Shelf (HTTP Server)
- Awesome Notifications
- Provider (State Management)
- WorkManager (Background Services)

### Server Built with:
- ESP32 Arduino framework
- Node.js & Express
- MongoDB
- Nodemailer
- Twilio
- PlatformIO

---

**Happy Monitoring! 🚀**
