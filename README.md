# Posture Monitor System

AI Agent 驱动构建的智能坐姿与环境感知监测系统。项目将 K230 视觉识别、ESP32-S3 边缘控制、OneNET 云端通信和 uni-app 移动端整合为一套端到端物联网系统，用于实时识别坐姿异常、感知人体在位与环境光状态，并通过本地提醒和 App 可视化反馈形成闭环。

## Highlights

- **AI 姿态识别**：K230 端运行 YOLOv8n-pose，基于人体关键点判断正常、低头、驼背、无人等状态。
- **多传感器融合**：ESP32-S3 融合 K230 姿态结果、人体存在检测和 BH1750 环境光数据。
- **端到端闭环**：视觉识别 → 串口传输 → 边缘判断 → MQTT 上云 → App 展示与控制。
- **跨端协议统一**：通过 `shared/protocol` 同步 TypeScript、C/C++、Python 和 JSON Schema 常量。
- **Agent 驱动开发**：使用多 Agent 工作流辅助需求拆解、代码重构、协议对齐、调试验证和安全清理。

## System Flow

```text
K230 Vision
Camera + YOLOv8n-pose
        |
        | UART / JSON
        v
ESP32-S3 Gateway
Posture fusion + Sensors + Local alerts
        |
        | MQTT / OneNET
        v
Cloud Platform
Device properties + History data
        |
        | HTTP / MQTT
        v
Mobile App
Realtime status + History + Control
```

## Tech Stack

| Module | Path | Tech |
|---|---|---|
| Vision | `k230/` | K230, MicroPython, YOLOv8n-pose |
| Firmware | `posture_monitor/` | ESP32-S3, Arduino C++, PubSubClient |
| App | `app/` | uni-app, Vue 3, TypeScript, Vite |
| Protocol | `shared/protocol/` | JSON Schema, TS/C/Python constants |
| Docs | `docs/` | Architecture, wiring, integration notes |

## Repository Structure

```text
.
├── app/                 # Mobile App
├── k230/                # K230 vision module
├── posture_monitor/     # ESP32-S3 firmware
├── shared/protocol/     # Cross-platform protocol contract
├── docs/                # System architecture and project docs
└── README.md
```

## Core Logic

1. K230 captures camera frames and performs pose estimation.
2. The visual module classifies posture as `normal`, `head_down`, `hunchback`, `unknown`, or `no_person`.
3. ESP32-S3 receives posture frames over UART and combines them with presence and ambient-light sensing.
4. The firmware reports device properties to OneNET through MQTT and triggers local LED / buzzer / voice alerts.
5. The mobile App reads realtime and historical data from OneNET and sends control commands back to the device.

## Agent-Driven Engineering

This project was developed with an AI Agent workflow rather than isolated code generation. Agents were used for:

- understanding and refactoring a multi-module repository;
- aligning protocol constants across App, ESP32, K230 and schema files;
- debugging cross-device communication and MQTT integration;
- reviewing security risks such as committed credentials, build artifacts and local test data;
- maintaining documentation for architecture, wiring and integration behavior.

The result is a complete AI-assisted engineering workflow covering planning, implementation, verification and repository hardening.

## Security Notes

Real credentials are not included in this repository. Configure local values through ignored environment files or local firmware configuration before deployment.

Examples:

- `app/.env`
- `posture_monitor/config.h`

Do not commit real Wi-Fi passwords, cloud tokens, generated firmware artifacts or personal test images.

## Documentation

- System architecture: `docs/architecture.md`
- Documentation index: `docs/index.md`
- ESP32 wiring: `posture_monitor/docs/hardware-wiring.md`
- K230 pinout: `k230/docs/k230-pinout.md`
- OneNET API: `app/docs/api/oneNet-api.md`
