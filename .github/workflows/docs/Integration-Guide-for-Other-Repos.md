# 🔗 Security Pipeline Integration Guide

This guide explains how other repositories can integrate with the centralized WebGoat Security Pipeline system.

## 🎯 Quick Start

### **Option 1: Full Integration (Recommended)**
Copy `templates/other-repo-security-integration.yml` to your repository:

```bash
# In your repository
mkdir -p .github/workflows
cp PATH/TO/WEBGOAT/.github/workflows/templates/other-repo-security-integration.yml .github/workflows/security-pipeline.yml
```

### **Option 2: Simple Integration**  
Copy `templates/simple-integration.yml` for basic functionality:

```bash
cp PATH/TO/WEBGOAT/.github/workflows/templates/simple-integration.yml .github/workflows/security.yml
```

## ⚙️ Configuration Steps

### **1. Update Repository Reference**
In your copied workflow file, update this line:
```yaml
uses: YOUR-ORG/webgoat/.github/workflows/security-pipeline-dispatcher.yml@main
```

**Replace with your actual WebGoat repository:**
```yaml
uses: mycompany/webgoat/.github/workflows/security/security-pipeline-dispatcher.yml@main
```

### **2. Set Up Personal Access Token (Required)**
The dispatcher needs API access to create issues and read Dependabot alerts.

#### **Create PAT:**
1. Go to GitHub → Settings → Developer settings → Personal access tokens
2. Create new token with scopes:
   - `repo` (Full repository access)
   - `security_events` (Read security events)
   - `read:org` (Read organization data)

#### **Add to Repository:**
1. In your repository: Settings → Secrets and variables → Actions
2. Add secret: `DEPENDABOT_PAT` = your PAT value

### **3. Enable Repository Features**
Ensure these are enabled in your repository settings:
- ✅ **Dependabot alerts** (Security → Dependabot alerts)
- ✅ **Issues** (General → Features)
- ✅ **Actions** (Actions → General)

## 🎛️ Available Trigger Options

### **Automatic Detection (Default)**
The dispatcher automatically determines which pipeline to run:

| **Trigger** | **Pipeline** | **Purpose** |
|-------------|--------------|-------------|
| Pull Request | Feature Branch Scan | Pre-merge vulnerability check |
| Push to Main | Main Branch Pipeline | Post-merge analysis & issue creation |
| Daily Schedule | Security Rescan | Vulnerability lifecycle tracking |
| Weekly Schedule | Metrics Dashboard | Compliance reporting |

### **Manual Control**
Override automatic detection by specifying `scan_type`:

```yaml
workflow_dispatch:
  inputs:
    scan_type:
      type: choice
      options:
        - auto          # Let dispatcher decide
        - feature-scan  # PR-level scanning
        - main-pipeline # Main branch analysis
        - rescan        # Daily vulnerability tracking
        - metrics       # Weekly reporting
        - all          # Run all applicable pipelines
```

## 📋 Integration Scenarios

### **Scenario 1: Basic Security Coverage**
**Use**: `simple-integration.yml`  
**Gets**: Automatic PR scanning, main branch analysis, daily tracking

```yaml
name: Security Scan
on:
  pull_request:
  push:
    branches: [ main ]
  schedule:
    - cron: '0 6 * * *'

jobs:
  security:
    uses: mycompany/webgoat/.github/workflows/security/security-pipeline-dispatcher.yml@main
    secrets:
      DEPENDABOT_PAT: ${{ secrets.DEPENDABOT_PAT }}
```

### **Scenario 2: Full Enterprise Integration**
**Use**: `other-repo-security-integration.yml`  
**Gets**: All pipelines + customization options + post-processing

```yaml
jobs:
  security-pipeline:
    uses: mycompany/webgoat/.github/workflows/security/security-pipeline-dispatcher.yml@main
    with:
      trigger_type: 'auto'
      target_branch: ${{ github.ref_name }}
      scan_type: ${{ inputs.scan_type || 'auto' }}
      repository_name: ${{ github.repository }}
    secrets:
      DEPENDABOT_PAT: ${{ secrets.DEPENDABOT_PAT }}
  
  custom-post-processing:
    needs: security-pipeline
    runs-on: ubuntu-latest
    steps:
      - name: Notify team
        run: echo "Security scan completed"
```

### **Scenario 3: Scheduled-Only Scanning**
For repositories that only want daily/weekly scans:

```yaml
name: Scheduled Security Scan
on:
  schedule:
    - cron: '0 6 * * *'    # Daily rescan
    - cron: '0 7 * * 0'    # Weekly metrics

jobs:
  security:
    uses: mycompany/webgoat/.github/workflows/security/security-pipeline-dispatcher.yml@main
    secrets:
      DEPENDABOT_PAT: ${{ secrets.DEPENDABOT_PAT }}
```

## 🎯 Dispatcher Input Parameters

| **Parameter** | **Description** | **Default** | **Options** |
|---------------|-----------------|-------------|-------------|
| `trigger_type` | How the workflow was triggered | `'auto'` | `pr`, `push`, `schedule`, `manual`, `auto` |
| `target_branch` | Branch being analyzed | `'main'` | Any branch name |
| `scan_type` | Specific pipeline to run | `'auto'` | `feature-scan`, `main-pipeline`, `rescan`, `metrics`, `all` |
| `repository_name` | Name of calling repo | Auto-detected | `owner/repo` format |

### **Example with Custom Parameters:**
```yaml
jobs:
  security:
    uses: mycompany/webgoat/.github/workflows/security/security-pipeline-dispatcher.yml@main
    with:
      trigger_type: 'manual'
      target_branch: 'develop'
      scan_type: 'feature-scan'
      repository_name: 'myteam/awesome-app'
    secrets:
      DEPENDABOT_PAT: ${{ secrets.DEPENDABOT_PAT }}
```

## 📊 What You Get

### **PR-Level Scanning (Feature Branch)**
- ✅ **Security Status Check**: Pass/fail status on PRs
- 📝 **Detailed Comments**: Vulnerability details in PR comments
- 🚦 **Merge Protection**: Optional blocking for security issues

### **Main Branch Analysis**
- 🎫 **Issue Creation**: GitHub issues for new vulnerabilities
- 📊 **Security Dashboard**: Real-time security status
- 🔗 **Dependabot Integration**: Direct links to alerts

### **Daily Vulnerability Tracking**
- 🔄 **Lifecycle Management**: Track from detection to resolution
- ✅ **Auto-Resolution**: Close issues when vulnerabilities are fixed
- 🛡️ **Status Reporting**: "All Clear" status when secure

### **Weekly Compliance Reports**
- 📈 **Trend Analysis**: Security posture over time
- 🎯 **Ecosystem Breakdown**: Vulnerabilities by package manager
- ⏱️ **Performance Metrics**: Resolution times and aging

## 🔧 Customization Options

### **Branch Protection Rules**
Protect main branch and require security status:
1. Repository Settings → Branches
2. Add rule for main branch
3. Enable "Require status checks to pass"
4. Select "Security Pipeline Dispatcher"

### **Custom Schedules**
Adjust scan frequency in your workflow:
```yaml
on:
  schedule:
    - cron: '0 9 * * 1,3,5'  # Monday, Wednesday, Friday at 9 AM
    - cron: '0 18 * * 0'     # Sunday at 6 PM
```

### **Path Filtering**
Only trigger on relevant file changes:
```yaml
on:
  push:
    branches: [ main ]
    paths:
      - 'package.json'
      - 'pom.xml'
      - 'requirements.txt'
      - 'Dockerfile'
```

### **Post-Processing Hook**
Add custom actions after security scan:
```yaml
jobs:
  security-pipeline:
    uses: mycompany/webgoat/.github/workflows/security-pipeline-dispatcher.yml@main
    secrets:
      DEPENDABOT_PAT: ${{ secrets.DEPENDABOT_PAT }}
  
  custom-actions:
    needs: security-pipeline
    if: always()
    runs-on: ubuntu-latest
    steps:
      - name: Send Slack notification
        if: failure()
        run: |
          curl -X POST -H 'Content-type: application/json' \
            --data '{"text":"Security scan failed for ${{ github.repository }}"}' \
            ${{ secrets.SLACK_WEBHOOK_URL }}
      
      - name: Update internal dashboard
        run: |
          # Custom logic to update your team's security dashboard
```

## 🛠️ Troubleshooting

### **Common Issues**

| **Issue** | **Cause** | **Solution** |
|-----------|-----------|--------------|
| "Workflow not found" | Wrong repository reference | Update `uses:` line with correct repo |
| "403 API Error" | Missing or invalid PAT | Set up `DEPENDABOT_PAT` secret |
| "No issues created" | Dependabot not enabled | Enable Dependabot alerts in repo settings |
| "Workflow doesn't trigger" | Missing permissions | Add required permissions to workflow |

### **Debug Steps**
1. **Check Workflow Logs**: Actions tab → View workflow run details
2. **Verify PAT**: Test PAT has required scopes and access
3. **Confirm Repository Access**: WebGoat repo must be accessible to calling repo
4. **Review Permissions**: Ensure all required permissions are granted

### **Test Your Integration**
```bash
# Test with a small PR
git checkout -b test-security-integration
echo "# Test" >> README.md
git add README.md
git commit -m "Test security integration"
git push origin test-security-integration

# Create PR and check for security status
```

## 📞 Support

### **Getting Help**
- 📋 **Check the logs**: Actions tab → Workflow runs → View details
- 📖 **Review documentation**: `Security-Pipeline-Workflow-Designs.md`
- 🐛 **Report issues**: Create issue in WebGoat repository
- 💬 **Ask questions**: Contact the Application Security Team

### **Advanced Configuration**
For advanced use cases like:
- Custom notification integrations
- Multi-repository coordination  
- Enterprise compliance reporting
- Custom vulnerability sources

Contact the security team for specialized setup guidance.

---

## 🚀 Ready to Get Started?

1. **Copy template workflow** to your repository
2. **Update repository reference** in the workflow
3. **Set up DEPENDABOT_PAT secret** in your repository
4. **Enable Dependabot alerts** in repository settings
5. **Test with a PR** to verify functionality
6. **Customize** based on your team's needs

**You'll have enterprise-grade security vulnerability management in minutes!** 🎉 