为您优化了README文件的结构和表述，使其更符合GitHub项目规范，同时保持了技术细节的准确性。

```markdown
# Experimental Code: TLS 1.3 Handshake with Hybrid Key Exchange from CPA-Secure KEMs

This repository contains the implementation and testing code for our paper **"On the Security and Efficiency of TLS 1.3 Handshake with Hybrid Key Exchange from CPA-Secure KEMs"**.

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
```

### Build & Install OpenSSL 3.6.0
```bash
# Download and extract
wget https://github.com/openssl/openssl/releases/download/openssl-3.6.0/openssl-3.6.0.tar.gz
tar -xzvf openssl-3.6.0.tar.gz
cd openssl-3.6.0

# Configure and compile
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl shared zlib
make
sudo make install

# Update library paths
echo "/usr/local/openssl/lib64" | sudo tee -a /etc/ld.so.conf.d/openssl.conf
sudo ldconfig

# Add to PATH (add to ~/.bashrc for persistence)
export PATH=/usr/local/openssl/bin:$PATH
source ~/.bashrc
```

### Verification
```bash
openssl version
# Expected output: OpenSSL 3.6.0 1 Oct 2025 (Library: OpenSSL 3.6.0 1 Oct 2025)
```

## 🔄 CPA-Secure ML-KEM Implementation

To test with the CPA-secure ML-KEM variant:

1. **Backup** the original implementation:
   ```bash
   cp /openssl-3.6.0/crypto/ml_kem/ml_kem.c /openssl-3.6.0/crypto/ml_kem/ml_kem.c.backup
   ```

2. **Replace** with CPA-secure version from this repository:
   ```bash
   cp /path/to/this/repo/ml_kem_cpa.c /openssl-3.6.0/crypto/ml_kem/ml_kem.c
   ```

3. **Recompile** OpenSSL:
   ```bash
   cd openssl-3.6.0
   make && sudo make install
   ```

> **Note**: Restore the backup file and recompile to return to the CCA-secure version.

## 📊 Performance Evaluation

### Algorithm-Level Benchmarking

Test raw cryptographic operations for each hybrid scheme:
```bash
openssl speed -seconds 3 <SCHEME>
```
Replace `<SCHEME>` with: `X25519MLKEM768`, `SecP256r1MLKEM768`, or `SecP384r1MLKEM1024`.

### Protocol-Level Benchmarking

#### 1. Generate Test Certificate
```bash
openssl ecparam -name prime256v1 -genkey -noout -out server.key
openssl req -new -x509 -sha256 -key server.key -out server.crt -days 365 -subj "/CN=localhost"
```

#### 2. Start TLS Server
```bash
openssl s_server -cert server.crt -key server.key -accept 4443 -www -groups <SCHEME>
```

#### 3. Measure Handshake Performance
```bash
openssl s_time -connect localhost:4443 -new -time 3
```

## ⚙️ TLS Groups Configuration

Enable support for all hybrid schemes in OpenSSL configuration:

#### Locate Configuration File
```bash
openssl version -d  # Output: OPENSSLDIR: "/usr/local/openssl"
```

#### Update `/usr/local/openssl/openssl.cnf`:
```ini
openssl_conf = openssl_init

[openssl_init]
ssl_conf = ssl_module

[ssl_module]
system_default = tls_system_default

[tls_system_default]
Groups = X25519MLKEM768:SecP256r1MLKEM768:SecP384r1MLKEM1024
```

> Alternatively, use the `openssl.cnf` file provided in this repository.

## 🤖 Automated Testing

### Algorithm-Level Tests
```bash
python3 runtime_KE.py <SCHEME>
```

### Protocol-Level Tests
```bash
python3 runtime_HS_loop.py <SCHEME>
```

Supported schemes: `X25519MLKEM768`, `SecP256r1MLKEM768`, `SecP384r1MLKEM1024`

## 📈 Results & Data

- Raw experimental data is available in the `data/` directory
- Files are prefixed with `CCA_` or `CPA_` to indicate the security version
- Data represents 50 iterations per configuration for statistical significance

> **Implementation Note**: Security version prefixes were manually assigned during post-processing, as the testing scripts do not auto-detect the ML-KEM variant.

## 📚 Reference

For detailed experimental methodology and results analysis, please refer to the accompanying paper.
