# Experimental Code: TLS 1.3 Handshake with Hybrid Key Exchange from CPA-Secure KEMs

This repository contains the implementation and testing code for the paper **"On the Security and Efficiency of TLS 1.3 Handshake with Hybrid Key Exchange from CPA-Secure KEMs"**.

The project enables reproduction of the performance evaluation experiments comparing CCA-secure and CPA-secure ML-KEM implementations within the TLS 1.3 handshake protocol.

## 🛠️ System Requirements

- **Operating System**: Ubuntu 24.04 LTS
- **Python**: Version 3.12.3
- **OpenSSL**: 3.6.0 (compiled from source)

## 📥 OpenSSL Installation & Configuration

### Prerequisites
```bash
sudo apt update
sudo apt install build-essential checkinstall zlib1g-dev -y
