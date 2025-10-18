# CIPHERWeb
WEB Penetration Testing Toolkit

# 🔒 Penetration Testing Toolkit - Quick Start Guide

## Installation

```bash
# Download the script
wget https://yourserver.com/pentest.sh
# OR
curl -O https://yourserver.com/pentest.sh

# Make it executable
chmod +x pentest.sh

# Create config file (first run will auto-create)
./pentest.sh -h
```

## Required Tools

### Core (Required)
```bash
# Ubuntu/Debian
sudo apt install -y nmap curl jq dnsutils whois

# RHEL/CentOS
sudo yum install -y nmap curl jq bind-utils whois
```

### Optional (Enhanced Features)
```bash
# Reconnaissance
sudo apt install -y amass subfinder httpx

# Enumeration
sudo apt install -y gobuster whatweb nikto wpscan

# Vulnerability Scanning
go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest

# Exploitation
sudo apt install -y sqlmap
```

## Quick Start Examples

### 1. Basic Reconnaissance (Safe)
```bash
./pentest.sh passive -t example.com
```
**What it does:** OSINT gathering, subdomain discovery, no direct interaction

### 2. Active Scanning
```bash
./pentest.sh active -t example.com --profile normal
```
**What it does:** Port scanning, service detection, OS fingerprinting

### 3. Full Vulnerability Assessment
```bash
./pentest.sh full -t https://example.com
```
**What it does:** Complete scan excluding exploitation (passive → active → enum → vuln)

### 4. Complete Penetration Test
```bash
./pentest.sh aggressive -t https://target.com -c
```
**What it does:** Everything including exploitation (requires -c confirmation)

### 5. Interactive Mode (Guided)
```bash
./pentest.sh interactive
```
**What it does:** Step-by-step guided workflow with prompts

## Command Reference

### Individual Phases
| Command | Description | Authorization Required |
|---------|-------------|----------------------|
| `passive` | OSINT & passive reconnaissance | No |
| `active` | Port scanning & service detection | No |
| `enum` | Web enumeration & fingerprinting | No |
| `vuln` | Vulnerability assessment | No |
| `exploit` | Active exploitation testing | **Yes (-c)** |
| `post` | Post-exploitation activities | **Yes (-c)** |
| `report` | Generate comprehensive report | No |

### Combined Modes
| Command | Description | Phases Included |
|---------|-------------|-----------------|
| `full` | Complete non-exploit scan | passive + active + enum + vuln |
| `aggressive` | Full pentest with exploitation | All phases |
| `interactive` | Guided workflow | User selected |

## Options

```bash
-t, --target <target>      Target domain or URL (required)
-o, --output <dir>         Custom output directory
-c, --confirm              Authorize active exploitation
-p, --profile <profile>    Scan profile: stealth|normal|aggressive
-v, --verbose              Enable verbose output
-h, --help                 Show help message
--version                  Show version
```

## Scan Profiles

### Stealth Profile
```bash
./pentest.sh active -t example.com --profile stealth
```
- Slow scanning (T2 timing)
- Top 100 ports only
- Minimal noise
- Ideal for: IDS/IPS evasion

### Normal Profile (Default)
```bash
./pentest.sh active -t example.com --profile normal
```
- Balanced speed (T3 timing)
- Top 1000 ports
- Standard approach
- Ideal for: Most assessments

### Aggressive Profile
```bash
./pentest.sh active -t example.com --profile aggressive
```
- Fast scanning (T4 timing)
- All 65535 ports
- UDP scanning included
- Ideal for: Time-critical assessments

## Authorization Setup

### Method 1: Config File (Recommended)
```bash
# Edit config file
nano config.txt

# Add authorized targets (one per line)
127.0.0.1
localhost
test.local
example.com
```

### Method 2: Confirmation Flag
```bash
# Use -c flag to bypass config check
./pentest.sh exploit -t example.com -c
```
**⚠️ Warning:** Only use on systems you own or have written permission to test

## Output Structure

```
pentest_results/
└── full_20250118_143022/
    ├── 01_passive_recon/
    │   ├── whois/              # Domain registration info
    │   ├── dns/                # DNS records
    │   ├── subdomains/         # Discovered subdomains
    │   ├── osint/              # OSINT data
    │   └── phase_summary.json
    ├── 02_active_recon/
    │   ├── nmap/               # Port scan results
    │   ├── services/           # Service details
    │   ├── ssl/                # SSL/TLS analysis
    │   └── phase_summary.json
    ├── 03_enumeration/
    │   ├── web/                # Web tech fingerprints
    │   ├── dirs/               # Directory listings
    │   ├── cms/                # CMS detection
    │   └── phase_summary.json
    ├── 04_vulnerabilities/
    │   ├── nuclei/             # Nuclei scan results
    │   ├── reports/
    │   │   ├── critical_findings.json
    │   │   ├── high_findings.json
    │   │   ├── medium_findings.json
    │   │   └── low_findings.json
    │   └── phase_summary.json
    ├── 05_exploitation/        # (if -c flag used)
    │   ├── sqli/
    │   ├── xss/
    │   └── phase_summary.json
    ├── 06_post_exploitation/   # (if -c flag used)
    └── final_report/
        ├── pentest_report.html
        ├── executive_summary.txt
        └── summary.json
```

## Common Workflows

### Workflow 1: External Web Application Test
```bash
# Step 1: Passive recon
./pentest.sh passive -t example.com -o ~/scans/example

# Step 2: Active scanning (stealth)
./pentest.sh active -t example.com -o ~/scans/example --profile stealth

# Step 3: Deep enumeration
./pentest.sh enum -t https://example.com -o ~/scans/example

# Step 4: Vulnerability assessment
./pentest.sh vuln -t https://example.com -o ~/scans/example

# Step 5: Generate report
./pentest.sh report -o ~/scans/example
```

### Workflow 2: Internal Network Assessment
```bash
# Quick aggressive scan
./pentest.sh full -t 192.168.1.100 --profile aggressive -o ~/internal_scan
```

### Workflow 3: WordPress Security Audit
```bash
# Target WordPress site
./pentest.sh full -t https://wordpress-site.com

# If authorized for exploitation
./pentest.sh exploit -t https://wordpress-site.com -c
```

## Report Generation

### Automatic Report Generation
Reports are automatically generated when running:
- `full` command
- `aggressive` command

### Manual Report Generation
```bash
# Generate report from existing scan
./pentest.sh report -o ./pentest_results/scan_20250118_143022
```

### Report Locations
- **HTML Report:** `final_report/pentest_report.html`
- **Executive Summary:** `final_report/executive_summary.txt`
- **JSON Summary:** `final_report/summary.json`

## Tips & Best Practices

### 1. Always Start Passive
```bash
# Gather intelligence without touching target
./pentest.sh passive -t example.com
```

### 2. Use Stealth for Production
```bash
# Less likely to trigger alerts
./pentest.sh active -t production.com --profile stealth
```

### 3. Test in Phases
```bash
# Run phases separately to control scope
./pentest.sh passive -t example.com
./pentest.sh active -t example.com
./pentest.sh enum -t example.com
```

### 4. Document Authorization
```bash
# Always add targets to config first
echo "authorized-target.com" >> config.txt
./pentest.sh full -t authorized-target.com
```

### 5. Review Before Exploit
```bash
# Review vuln findings before exploitation
./pentest.sh vuln -t example.com
cat results/04_vulnerabilities/reports/critical_findings.json
# Only then proceed with exploitation if authorized
./pentest.sh exploit -t example.com -c
```

## Troubleshooting

### Issue: "Target not authorized"
```bash
# Solution 1: Add to config
echo "example.com" >> config.txt

# Solution 2: Use confirmation flag
./pentest.sh passive -t example.com -c
```

### Issue: "Missing tools"
```bash
# Check what's missing
./pentest.sh passive -t example.com

# Install suggested tools
sudo apt install -y <missing-tool>
```

### Issue: "Permission denied"
```bash
# Make script executable
chmod +x pentest.sh

# Some tools need root (optional)
sudo ./pentest.sh active -t example.com
```

### Issue: Scans timing out
```bash
# Use longer timeouts or stealth profile
./pentest.sh active -t example.com --profile stealth
```

## Legal & Ethical Guidelines

### ⚠️ CRITICAL WARNINGS

1. **Authorization Required**
   - Only test systems you own
   - Get written permission for third-party systems
   - Unauthorized access is illegal

2. **Scope Limitations**
   - Stay within authorized scope
   - Don't pivot to unauthorized systems
   - Respect rate limits

3. **Data Handling**
   - Encrypt sensitive findings
   - Store reports securely
   - Follow data protection regulations

4. **Responsible Disclosure**
   - Report vulnerabilities responsibly
   - Give vendors time to patch
   - Don't publish exploits publicly

## Advanced Usage

### Custom Wordlists
```bash
# Edit script to use custom wordlists
# Look for WORDLIST variable in functions
```

### Parallel Scanning
```bash
# Scan multiple targets
for target in target1.com target2.com target3.com; do
    ./pentest.sh full -t $target -o ./scans/$target &
done
wait
```

### Integration with CI/CD
```bash
#!/bin/bash
# Add to your pipeline
./pentest.sh vuln -t staging.example.com
if [ $(jq '.summary.critical' results/latest/04_vulnerabilities/phase_summary.json) -gt 0 ]; then
    echo "Critical vulnerabilities found - blocking deployment"
    exit 1
fi
```

## Support & Resources

- **Documentation:** Run `./pentest.sh --help`
- **Issues:** Check script output and logs
- **Updates:** Pull latest version regularly
- **Community:** Share findings responsibly

## Version History

- **v3.0** - Complete 6-phase framework with reporting
- **v2.0** - Added interactive mode and profiles
- **v1.0** - Initial release

---

**Remember:** With great power comes great responsibility. Always test ethically and legally! 🔒
