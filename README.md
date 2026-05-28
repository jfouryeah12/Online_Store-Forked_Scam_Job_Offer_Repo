# 🚨 Online Store – Malware Analysis Repository

> **⚠️ CRITICAL WARNING: DO NOT RUN THIS ON YOUR MAIN WORKSTATION**  
> **USE A DISPOSABLE VIRTUAL MACHINE OR ISOLATED SANDBOX ONLY**

---

## 📌 Purpose

This repository is a **fork of a malicious scam repository** provided for **educational and security research purposes only**.

The original repository was distributed to developers via fake job opportunities on:

- **OnlineJob**
- **Hubstaff Talent**
- **Wellfound (formerly AngelList)**

The attacker poses as a Western recruiter (actually operating from **India**) and tricks developers into cloning and running this code as a "technical test" or "sample project."

---

## 🧠 Attack Summary

| Attribute | Details |
|-----------|---------|
| **Attack Type** | Supply Chain / Typosquatting Malware |
| **Trigger** | `npm install` |
| **Malicious Package** | `tailwind-animator` (typosquat of legitimate `tailwindcss-animate`) |
| **Where Malware Lives** | `node_modules/tailwind-animator/src/index.js` |
| **Obfuscation** | 4+ layers (Base64, custom cipher, blockchain C2) |
| **C2 Infrastructure** | Tron Blockchain + Aptos + Ethereum JSON-RPC |
| **Persistence** | Detached hidden Node.js child process |
| **Primary Goal** | Environment variable harvesting, credential theft, backdoor installation |

---

## 🔬 What Happens When You Run `npm install`

1. **Typosquatting Package Installed**  
   `tailwind-animator` is pulled from npm instead of the legitimate `tailwindcss-animate`.

2. **Malicious Payload Executes**  
   The `index.js` file contains an obfuscated `eval(atob(...))` loader.

3. **Blockchain C2 Contact**  
   The malware reads a wallet address on the Tron blockchain to fetch the real C2 URL.

4. **Secondary Payload Download**  
   Encrypted code is fetched and decrypted via Ethereum JSON-RPC.

5. **Persistent Backdoor**  
   A hidden, detached Node.js process is spawned (`detached: true`, `windowsHide: true`, `stdio: 'ignore'`).

6. **Data Exfiltration**  
   Environment variables, `.env` files, and system information are harvested and sent to the attacker.

---

## 🕵️ Indicators of Compromise (IOCs)

### Malicious Package
| Package Name | Legitimate Target |
|--------------|-------------------|
| `tailwind-animator` | `tailwindcss-animate` |

### Blockchain C2 Addresses
| Blockchain | Address |
|------------|---------|
| **Tron** | `TMfKQEd7TJJa5xNZJZ2Lep838vrzrs7mAP` |
| **Aptos** | `0xe66ae4c5e9516048911b3ade1bc8b258197259604c1206cfeca01451a7c22e6d` |

### C2 Domains / IPs
| Type | Value |
|------|-------|
| IP | `166.88.4[.]2` |
| IP | `136.0.9[.]8` |
| Domain (Vercel) | `tetrismic[.]vercel[.]app` |

### Process Detection
- Hidden Node.js process with command line starting with:  
  `node -e "global['_V']='A11';global['r']=require;global['m']=module;(async()=>{...`

---

## 🛡️ How to Protect Yourself

### ✅ Before Running Any Unknown Repository

```bash
# 1. Always audit package.json for typosquatting
npm install --package-lock-only
npx typoguard

# 2. Install without running lifecycle scripts
npm install --ignore-scripts

# 3. Scan with security tools
npx ghsafe scan ./
npx skillrx ./

# 4. Check dependency download counts (low = suspicious)
npm info <package-name> weekly-downloads
