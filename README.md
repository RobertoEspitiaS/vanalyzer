# vAnalyzer - Enterprise Vulnerability Reporting Platform

[![Version](https://img.shields.io/badge/version-1.4-blue)](https://github.com/vanalyzer/vanalyzer)
[![Docker](https://img.shields.io/badge/docker-ready-brightgreen)](https://www.docker.com/)
[![License](https://img.shields.io/badge/license-proprietary-red)](LICENSE)

## 🚀 Current Status: v1.4 - Enhanced Analytics Edition

### Latest Features
- ✅ **VulnCheck Integration**: Advanced threat intelligence with weaponization tracking
- ✅ **Multi-Source Enrichment**: Automated KEV and EPSS data integration
- ✅ **CVRA Scoring Engine**: Composite risk assessment with dynamic threat multipliers
- ✅ **Dual Patch Management**: Support for both legacy and modern patch systems
- ✅ **Unified Installer**: Single command deployment across all Linux distributions

## 🎯 Installation and Configuration Guide

### What is vAnalyzer?
vAnalyzer is an enterprise vulnerability reporting platform that integrates with Vicarius vRx to provide comprehensive risk assessment and prioritization. The platform enriches vulnerability data with multiple threat intelligence sources including CISA KEV, FIRST EPSS, and optional VulnCheck integration for advanced threat tracking.

### Key Features
- **CVRA Risk Scoring**: Composite Vulnerability Risk Assessment combining multiple data sources
- **Threat Intelligence**: Automated integration with CISA KEV, FIRST EPSS, and VulnCheck
- **Multi-dimensional Analysis**: Comprehensive evaluation of severity, exploitability, and exposure
- **Smart Prioritization**: Dynamic risk-based ranking with threat urgency multipliers (up to 2.5x)
- **Containerized Architecture**: Docker Swarm deployment with automatic orchestration
- **Analytics Dashboard**: Metabase-powered visualizations with custom reporting capabilities

### System Requirements

#### Hardware
- **CPU**: 4+ cores recommended
- **RAM**: 8GB minimum, 16GB recommended
- **Storage**: 50GB minimum, 100GB+ for extended data retention
- **Network**: Stable internet connection for API access

#### Software
- **Operating System**: Linux (Ubuntu 20.04+, RHEL 8+, Rocky Linux 8+, AlmaLinux 8+)
- **Docker**: Version 20.10+ (installer will handle if not present)
- **Docker Compose**: Version 2.0+ (included with Docker)

#### Required Credentials
1. **Vicarius vRx Access**:
   - Dashboard URL or ID (e.g., "https://company.vicarius.cloud" or "company")
   - API Key (from Vicarius dashboard: Settings → API Keys)
2. **Optional - VulnCheck API**:
   - API Key for enhanced threat intelligence (if subscribed)
3. **System Access**:
   - Root or sudo privileges for installation

---

## 📦 Installation Steps

### Step 1: Download and Prepare
```bash
# Clone repository or extract archive
git clone https://github.com/vanalyzer/vanalyzer.git
# OR if using archive:
tar -xzf vanalyzer1.4.tar.gz

# Navigate to directory
cd vanalyzer1.4-new  # or vanalyzer1.4-main if from archive

# Make installer executable
chmod +x vanalyzer
```

### Step 2: Install Docker Runtime (if needed)
```bash
sudo ./vanalyzer install
```

This command will:
- Detect your OS and install appropriate Docker version
- Configure Docker service and enable at startup
- Initialize Docker Swarm mode for orchestration
- Add current user to docker group (logout/login required)

### Step 3: Interactive Configuration
```bash
sudo ./vanalyzer init
```

**Configuration wizard prompts:**

| Setting | Description | Example/Default |
|---------|-------------|----------------|
| **Hostname** | FQDN for SSL certificate | `vanalyzer.company.com` |
| **Dashboard ID** | Vicarius instance identifier | `company` or full URL |
| **API Key** | Vicarius API authentication | 40-character key |
| **Database Name** | PostgreSQL database | Default: dashboard ID |
| **Database User** | PostgreSQL username | Default: `vanalyzer` |
| **Database Password** | Strong password required | Min 12 characters |
| **Sync Interval** | Hours between data refresh | Default: `6` |
| **VulnCheck API** | Optional threat intelligence | Skip if not subscribed |

**Note**: Configuration is saved to `.env` file and Docker secrets

### Step 4: Deploy Stack
```bash
sudo ./vanalyzer deploy
```

**Deployment process:**
1. Creates Docker overlay network
2. Generates SSL certificates
3. Initializes PostgreSQL database
4. Deploys application stack:
   - `app`: Python data processor and sync engine
   - `appdb`: PostgreSQL database with optimized indexes
   - `metabase`: Analytics dashboard and reporting
   - `traefik`: Reverse proxy with SSL termination
5. Starts initial data synchronization

⏱️ **Expected time**: 5-10 minutes for full deployment

### Step 5: Configure DNS (if using custom hostname)
Add to your hosts file:
- **Windows**: `C:\Windows\System32\drivers\etc\hosts`
- **Mac/Linux**: `/etc/hosts`

Add line: `YOUR_SERVER_IP your-hostname`

### Step 6: Access Dashboard
1. Navigate to: `https://[your-hostname]`
2. Accept certificate warning (self-signed cert is expected)
3. Login with default credentials:
   
   ⚠️ **First Login**: Change these credentials immediately!
   ```
   Username: vrxadmin@vrxadmin.com
   Password: Vicarius123!@#
   ```

### Step 7: Initial Configuration

**Complete these steps after first login:**

1. **Update admin password**:
   - Navigate to: **Settings → Account settings**
   - Set a strong password

2. **Configure database connection**:
   - Navigate to: **Settings → Admin Settings → Databases**
   - Select **vRX-Reports** database
   - Enter credentials from Step 3:
     - **Database Name**: Your configured database name
     - **Username**: Database user from configuration
     - **Password**: Database password from configuration
   - Click **Save** and test connection

3. **Verify data synchronization**:
   - Navigate to **Browse Data** section
   - Check for vulnerability records
   - Initial sync completes in 15-30 minutes

---

## 🔧 Daily Operations

### Essential Commands

#### System Monitoring
```bash
sudo ./vanalyzer status    # Check all services status
sudo ./vanalyzer health    # Run comprehensive health checks
sudo ./vanalyzer validate  # Validate configuration and API access
```

#### Log Management
```bash
sudo ./vanalyzer logs app       # Application sync logs
sudo ./vanalyzer logs appdb     # Database logs
sudo ./vanalyzer logs metabase  # Dashboard logs
sudo ./vanalyzer logs traefik   # Proxy/SSL logs
```

#### Data Management
```bash
sudo ./vanalyzer backup         # Create timestamped backup
sudo ./vanalyzer restore <dir>  # Restore from backup
sudo ./vanalyzer update         # Force immediate data sync
```

### Data Synchronization
- **Automatic sync**: Runs at configured interval (default: 6 hours)
- **Manual sync**: `sudo ./vanalyzer update`
- **Monitor sync**: `sudo ./vanalyzer logs app | grep -i sync`
- **Sync includes**:
  - Vulnerability data from Vicarius vRx
  - KEV catalog updates from CISA
  - EPSS scores from FIRST
  - VulnCheck enrichment (if configured)

---

## 🚀 Offline Deployment

### Step 1: Create Bundle (Internet-Connected Machine)
```bash
sudo ./vanalyzer deploy     # Deploy and pull images
sudo ./vanalyzer bundle     # Creates offline package with all images
```

### Step 2: Deploy Offline (Air-Gapped Server)
```bash
sudo ./vanalyzer import     # Import Docker images from bundle
sudo ./vanalyzer init       # Configure application
sudo ./vanalyzer deploy     # Deploy using imported images
```

**Note**: Offline deployments won't receive external threat intelligence updates

---

## 🛠️ Troubleshooting Guide

### Quick Diagnostics
```bash
# Full system check
sudo ./vanalyzer health

# Validate configuration
sudo ./vanalyzer validate

# Service status
sudo ./vanalyzer status
```

### Common Issues

#### "Command not found"
```bash
cd vanalyzer1.4-main
chmod +x vanalyzer
```

#### "Permission denied"
Use `sudo` with all commands.

#### Certificate Warning
Normal for self-signed certificates. Click "Advanced" → "Proceed".

#### No Data in Dashboards
1. Check sync status:
   ```bash
   sudo ./vanalyzer logs app | grep -i sync
   ```
2. Verify API connectivity:
   ```bash
   sudo ./vanalyzer validate
   ```
3. Force manual sync:
   ```bash
   docker exec $(docker ps -q -f name=app) python /app/scripts/launcher.py
   ```
4. Wait 15-30 minutes for initial data population

#### Can't Access Website
- Check services: `sudo ./vanalyzer status`
- Verify hostname in hosts file
- Try IP address directly: `https://YOUR_SERVER_IP`

#### Login Issues
- Default credentials: `vrxadmin@vrxadmin.com` / `Vicarius123!@#`
- Wait 5 minutes after deployment for services to start

---

## 🎯 Risk Scoring Methodology

### CVRA (Composite Vulnerability Risk Assessment)
vAnalyzer's proprietary scoring system provides:

- **Composite Risk Score**: 0-100 scale with 2-decimal precision
- **Risk Classifications**: CRITICAL (75+), HIGH (50-74.99), MEDIUM (25-49.99), LOW (0-24.99)
- **Dynamic Adjustment**: Real-time threat urgency multipliers based on exploitation activity

### Risk Components (Weighted Scoring)

| Component | Weight | Description | Data Source |
|-----------|--------|-------------|-------------|
| **KEV Factor** | 29.2% | Known exploited vulnerabilities | CISA KEV Catalog |
| **EPSS Factor** | 25.0% | Exploit prediction probability | FIRST EPSS |
| **CVSS Factor** | 20.8% | Base vulnerability severity | NVD/Vicarius |
| **Patch Factor** | 16.7% | Patch availability & age | Vicarius vRx |
| **Volume Factor** | 8.3% | Attack surface exposure | Asset count |

### Threat Urgency Multipliers

| Threat Level | Multiplier | Criteria |
|--------------|------------|----------|
| **Critical** | 2.5x | Weaponized exploit + Active ransomware campaign |
| **Severe** | 2.0x | In KEV catalog OR Exploited by APT groups |
| **High** | 1.5x | Public exploit available OR EPSS > 50% |
| **Elevated** | 1.2x | EPSS 20-50% OR PoC exploit exists |
| **Baseline** | 1.0x | No active exploitation detected |

### External Data Sources

#### Integrated by Default
- **CISA KEV**: Known Exploited Vulnerabilities
  - URL: `https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json`
  - Update frequency: Every sync cycle
  - Data: CVE ID, vendor, product, vulnerability name, date added

- **FIRST EPSS**: Exploit Prediction Scoring
  - URL: `https://epss.empiricalsecurity.com/epss_scores-current.csv.gz`
  - Update frequency: Every sync cycle
  - Data: CVE ID, EPSS score, percentile ranking

#### Optional Integration
- **VulnCheck**: Advanced Threat Intelligence (requires API key)
  - Exploit maturity and weaponization status
  - Ransomware campaign associations
  - Threat actor attribution
  - Exploit kit integration

Data synchronization runs automatically based on configured interval (default: 6 hours).

---

## 🔐 Security Notes

### SSL Certificates
- Default: Self-signed certificates (browser warning expected)
- Custom certificates: Place in `certs/` directory
- Certificate management: `sudo ./vanalyzer certs`

### Backup Strategy
```bash
# Manual backup
sudo ./vanalyzer backup

# Backups stored in: ./backups/
```

### Complete Removal
```bash
sudo ./vanalyzer purge    # WARNING: Removes everything including data
```

---

## ⚡ Quick Reference

### Setup & Deployment
| Task | Command |
|------|---------|
| Install Docker | `sudo ./vanalyzer install` |
| Initial setup | `sudo ./vanalyzer init` |
| Deploy system | `sudo ./vanalyzer deploy` |
| Update system | `sudo ./vanalyzer update` |

### Management & Monitoring
| Task | Command |
|------|---------|
| Check status | `sudo ./vanalyzer status` |
| View logs | `sudo ./vanalyzer logs [service]` |
| Health check | `sudo ./vanalyzer health` |
| Validate config | `sudo ./vanalyzer validate` |

### Data Management
| Task | Command |
|------|---------|
| Backup data | `sudo ./vanalyzer backup` |
| Restore data | `sudo ./vanalyzer restore <backup_directory>` |

### Maintenance
| Task | Command |
|------|---------|
| Clean logs | `sudo ./vanalyzer clean-logs` |
| Clean Docker | `sudo ./vanalyzer clean-docker` |
| Remove all | `sudo ./vanalyzer purge` |

### Offline Deployment
| Task | Command |
|------|---------|
| Create bundle | `sudo ./vanalyzer bundle` |
| Import bundle | `sudo ./vanalyzer import [file]` |

### Utilities
| Task | Command |
|------|---------|
| Manage certs | `sudo ./vanalyzer certs` |
| Show version | `sudo ./vanalyzer version` |
| Show help | `sudo ./vanalyzer help` |

**Services**: `app`, `appdb`, `metabase`, `traefik`

---

## 📝 Post-Installation Checklist

- [ ] Changed default Metabase admin password
- [ ] Configured database connection in Metabase settings
- [ ] Verified initial data sync completed (15-30 minutes)
- [ ] Set up backup schedule (recommended: daily)
- [ ] Configured DNS for hostname (if using custom domain)
- [ ] Tested VulnCheck integration (if API key provided)
- [ ] Reviewed CVRA risk scores in dashboard
- [ ] Documented custom configurations

## 💡 Best Practices

### Operations
- Schedule regular backups using cron
- Monitor disk usage for database growth
- Review sync logs weekly for errors
- Test restore procedures quarterly

### Performance
- Allocate sufficient RAM for large datasets
- Use SSD storage for database performance
- Adjust sync interval based on data volume
- Archive old data periodically

### Dashboard Usage
- Create custom dashboards for different teams
- Set up automated reports for stakeholders
- Use filters to focus on critical assets
- Export data for compliance reporting

## 📚 Additional Resources

- **Command Reference**: Run `sudo ./vanalyzer help`
- **Service Logs**: Access via `sudo ./vanalyzer logs <service>`
- **Backup/Restore**: See maintenance commands section
- **API Documentation**: [Vicarius vRx API](https://api.vicarius.io)

---

**Version**: 1.4 | **Branch**: vulncheck-integration | **Last Updated**: 2025-09-07