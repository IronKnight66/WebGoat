# WebGoat Security Pipeline Documentation

This repository implements a comprehensive GitHub Dependabot security pipeline designed to automatically detect, track, and resolve security vulnerabilities in the WebGoat project using GitHub's native security features.

## 🏗️ Architecture Overview

The security pipeline consists of four main components:

1. **Enhanced Dependabot Configuration** - Automated dependency updates with security focus
2. **Feature Branch Security Scanning** - PR-level vulnerability detection using Dependabot alerts
3. **Main Branch Security Pipeline** - Post-merge vulnerability tracking and issue creation
4. **Security Rescan Scheduler** - Automated vulnerability resolution tracking and closure
5. **Security Metrics Dashboard** - Comprehensive reporting and compliance monitoring

## 📋 Prerequisites

Before setting up the pipeline, ensure you have:

- [x] Repository permissions for creating issues and managing security events
- [x] Dependabot alerts enabled for the repository

## 🔧 Setup Instructions

### 1. Configure Repository Settings

#### Enable Required Features
1. Go to **Settings** → **Security & analysis**
2. Enable **Dependency graph**
3. Enable **Dependabot alerts**  
4. Enable **Dependabot security updates**

#### Repository Permissions
Ensure the following permissions are enabled:
- `contents: read` - Access repository code
- `issues: write` - Create/update/close issues
- `pull-requests: write` - Add comments and status checks
- `security-events: read` - Access Dependabot alerts
- `actions: read` - Access workflow metadata

### 2. Configure Team Members

Update the assignee settings in all workflow files:

1. **`.github/dependabot.yml`** - Replace `IronKnight66` with your security team members
2. **Workflow files** - Update assignees in issue creation sections

### 3. Customize Severity Thresholds

The pipeline uses the following default severity handling:

- **Critical/High**: Blocks PR merges, creates immediate alerts
- **Medium**: Warnings with detailed information  
- **Low**: Informational only

## 🚀 Pipeline Components

### Enhanced Dependabot Configuration

**File**: `.github/dependabot.yml`

**Features**:
- Daily security-focused dependency scanning
- Separate high-frequency security-only updates  
- Grouped updates by security libraries, frameworks, and testing dependencies
- Automatic labeling and assignment of security patches

**Key Configuration**:
```yaml
# Security-only updates (high frequency)
- package-ecosystem: "maven"
  schedule:
    interval: "daily"
    time: "02:00"
  allow:
    - dependency-type: "direct"
      update-types: ["security"]
  labels: ["dependencies", "security-patch", "critical", "auto-merge-candidate"]
```

### Feature Branch Security Scanning

**File**: `.github/workflows/feature-branch-security-scan.yml`

**Triggers**:
- Pull request events (opened, synchronize, reopened)
- Changes to dependency files (`pom.xml`, `Dockerfile`, etc.)

**Workflow Steps**:
1. **Build Analysis** - Compiles project and analyzes dependencies
2. **Dependabot Alert Check** - Retrieves current vulnerability alerts
3. **Severity Filtering** - Categorizes findings by risk level
4. **PR Feedback** - Posts detailed vulnerability reports as comments
5. **Status Checks** - Blocks merges for high/critical issues

**Sample PR Comment**:
```markdown
🚨 **Dependabot Security Vulnerability Scan Results**

**Summary**: Found 2 vulnerabilities (1 High, 1 Medium)

**🔴 High Severity Issues:**
- **spring-core** - CVE-2022-22965
  - **Impact**: Spring Framework RCE vulnerability...
  - **CVSS Score**: 9.8
  - **Patched Version**: 5.3.19+
  - **Alert**: [View Details](https://github.com/org/repo/security/dependabot/123)

**🔧 Remediation Steps:**
1. Review the Dependabot alerts in the Security tab
2. Accept Dependabot PRs to update vulnerable dependencies
3. Test changes thoroughly before merging
```

### Main Branch Security Pipeline

**File**: `.github/workflows/main-branch-security-pipeline.yml`

**Triggers**:
- Push to main branch
- Manual workflow dispatch

**Key Features**:
- **Change Detection** - Identifies new vulnerabilities vs. previous scans
- **Issue Creation** - Creates GitHub issues for each new Dependabot alert
- **Severity-Based SLAs** - Sets resolution timelines based on risk level
- **Dashboard Updates** - Maintains security metrics and status

**Issue Template**:
```markdown
## Security Vulnerability Alert

**CVE ID**: `CVE-2022-12345`
**Package**: `vulnerable-library`
**Severity**: **HIGH**
**CVSS Score**: 8.1

### Remediation Plan
- [ ] Review Dependabot alert #123
- [ ] Accept Dependabot PR if available
- [ ] Or manually update to patched version
- [ ] Test application functionality
- [ ] Verify fix resolves alert

### Dependabot Alert
🔗 **[View Dependabot Alert #123](https://github.com/org/repo/security/dependabot/123)**
```

### Security Rescan Scheduler

**File**: `.github/workflows/security-rescan-scheduler.yml`

**Triggers**:
- Daily scheduled runs (3 AM UTC)
- After Dependabot PRs are merged
- Manual workflow dispatch

**Scan Types**:
- **Full Scan** - Weekly comprehensive analysis (Sundays)
- **Incremental Scan** - Daily focused scanning  
- **Critical-Only** - Emergency high-severity scanning

**Auto-Closure Logic**:
1. Retrieves current Dependabot alerts
2. Compares with open security issues
3. Automatically closes resolved issues when alerts are no longer active
4. Updates overdue issues with escalation notices

### Security Metrics Dashboard

**File**: `.github/workflows/security-metrics-dashboard.yml`

**Triggers**:
- Weekly scheduled reports (Monday 9 AM UTC)
- Manual workflow dispatch with report type selection

**Report Types**:
- **Comprehensive** - Full analysis with all metrics
- **Summary** - Key indicators and trends
- **Compliance** - SLA adherence and policy compliance
- **Trends** - Historical analysis and patterns

**Generated Artifacts**:
- 📊 Visual dashboard (PNG charts)
- 📋 Detailed metrics (JSON data)
- 🏛️ Compliance report (Markdown)
- 👔 Executive summary (Markdown)
- 🎫 Security badge (JSON for README)

## 📊 Metrics and KPIs

The pipeline tracks the following key performance indicators:

### Security Metrics
- **Total Vulnerabilities**: All security issues detected
- **Open vs Closed**: Current security posture  
- **By Severity**: Critical, High, Medium, Low breakdown
- **Overdue Issues**: Items exceeding target resolution time
- **Dependabot Alerts**: Currently active vulnerability alerts

### Performance Metrics  
- **Mean Time to Detection (MTTD)**: How quickly vulnerabilities are identified
- **Mean Time to Resolution (MTTR)**: Average time from detection to fix
- **Scan Success Rate**: Reliability of security scanning pipeline
- **Scan Frequency**: How often security scans are performed

### Compliance Metrics
| Metric | Target | Monitoring |
|--------|---------|------------|
| Critical Issue Resolution | ≤ 3 days | ✅ Automated |
| High Issue Resolution | ≤ 7 days | ✅ Automated |
| Medium Issue Resolution | ≤ 30 days | ✅ Automated |
| Overdue Issues | 0 | ✅ Automated |
| Scan Success Rate | ≥ 95% | ✅ Automated |

## 🔐 Security Best Practices

### Severity-Based Response Times
- **Critical (CVSS 9.0-10.0)**: 3 days
- **High (CVSS 7.0-8.9)**: 7 days  
- **Medium (CVSS 4.0-6.9)**: 30 days
- **Low (CVSS 0.1-3.9)**: 90 days

### Automated Actions
- **Critical/High**: Block PR merges, create immediate issues
- **Medium**: Warning comments, tracking issues
- **Low**: Informational logging only

### Escalation Process
1. **Day 0**: Vulnerability detected via Dependabot, issue created
2. **Target Date**: Automated reminder if unresolved
3. **Overdue**: Escalation labels added, management notification
4. **Resolution**: Automatic issue closure when Dependabot alert resolves

## 🛠️ Customization Options

### Modifying Scan Frequency
Edit the cron schedules in workflow files:

```yaml
schedule:
  # Daily at 3 AM UTC
  - cron: '0 3 * * *'
  
  # Weekly on Mondays at 9 AM UTC  
  - cron: '0 9 * * 1'
```

### Adjusting Severity Filtering
Modify the severity filtering logic in the workflow steps to change which alerts trigger actions.

### Customizing Issue Templates
Edit the issue creation scripts in the workflow files to modify:
- Issue titles and formatting
- Assignees and labels
- Resolution timelines
- Escalation procedures

### Adding Notifications
Integrate with external systems by adding notification steps:

```yaml
- name: Notify Slack
  if: steps.get-alerts.outputs.high_count > 0
  uses: 8398a7/action-slack@v3
  with:
    status: failure
    text: "High severity vulnerabilities detected in WebGoat"
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

## 🚨 Troubleshooting

### Common Issues

#### 1. Dependabot Alerts Not Appearing
**Problem**: Pipeline not detecting vulnerabilities  
**Solution**: 
- Verify Dependabot is enabled in repository settings
- Check that dependency graph is enabled
- Ensure repository has supported package files (pom.xml, package.json, etc.)

#### 2. False Positives in Issue Creation
**Problem**: Issues created for resolved vulnerabilities  
**Solution**:
- Review Dependabot alert state synchronization
- Check issue closure logic in rescan scheduler
- Verify CVE matching logic between alerts and issues

#### 3. Workflow Permission Errors  
**Problem**: GitHub API permission issues
**Solution**:
- Verify repository has required permissions enabled
- Check workflow token permissions in YAML files
- Ensure team members have appropriate repository access

#### 4. Build Failures During Scanning
**Problem**: Maven compilation errors during dependency analysis
**Solution**:
- Check Java version compatibility (using JDK 21)
- Review Maven cache configuration
- Ensure all required dependencies are available

### Debugging Workflow Issues

1. **Check Workflow Logs**: Review detailed step outputs in GitHub Actions
2. **Verify Dependabot Alerts**: Check Security tab for active alerts
3. **Review Artifacts**: Download and examine generated reports  
4. **Test API Access**: Verify GitHub API permissions for Dependabot endpoints

## 📈 Advanced Configuration

### Multi-Environment Support
Configure different scanning profiles for different environments:

```yaml
strategy:
  matrix:
    environment: [development, staging, production]
    include:
      - environment: production
        alert_threshold: medium
        scan_frequency: daily
      - environment: development  
        alert_threshold: high
        scan_frequency: weekly
```

### Integration with External Tools

#### JIRA Integration
```yaml
- name: Create JIRA Issue
  if: steps.get-alerts.outputs.high_count > 0
  uses: atlassian/gajira-create@v2
  with:
    project: SEC
    issuetype: Bug
    summary: "Security vulnerability detected in WebGoat"
```

#### Microsoft Teams Integration  
```yaml
- name: Teams Notification
  uses: jdcargile/ms-teams-notification@v1.3
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    ms-teams-webhook-uri: ${{ secrets.MS_TEAMS_WEBHOOK_URI }}
    notification-summary: "Security Alert: WebGoat"
```

## 🔍 Monitoring and Alerts

### GitHub Repository Insights
- Monitor workflow success rates in **Actions** tab
- Review security alerts in **Security** → **Dependabot** tab  
- Track issue resolution in **Issues** tab with security labels

### Dashboard Integration
The pipeline generates data suitable for integration with:
- **GitHub Projects** - Security vulnerability tracking board
- **Grafana** - Custom security metrics dashboards  
- **DataDog** - APM and security monitoring
- **Splunk** - Log analysis and alerting

### Automated Reporting
Weekly executive reports can be automatically:
- Posted to designated Slack channels
- Emailed to security team distribution lists
- Uploaded to shared document repositories
- Integrated with corporate dashboards

## 🤝 Contributing

### Adding New Scan Types
1. Create new workflow file in `.github/workflows/`
2. Follow existing naming convention: `security-[purpose].yml`
3. Include proper permissions and error handling
4. Add documentation to this README

### Modifying Existing Workflows
1. Test changes in a fork first
2. Ensure backward compatibility with existing issues
3. Update documentation for any configuration changes
4. Consider impact on existing SLAs and metrics

## 📞 Support

### Getting Help
- **Internal**: Contact your Application Security Team
- **GitHub**: Review [GitHub Advanced Security documentation](https://docs.github.com/en/code-security)
- **Dependabot**: Consult [Dependabot documentation](https://docs.github.com/en/code-security/dependabot)

### Escalation Process
1. **Level 1**: Development team reviews and addresses
2. **Level 2**: Security team consultation and guidance  
3. **Level 3**: Management escalation for policy exceptions
4. **Level 4**: External security consultation if needed

---

## 📝 Change Log

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2024-01-15 | Initial pipeline implementation |
| 1.1.0 | 2024-01-20 | Added compliance reporting |
| 1.2.0 | 2024-01-25 | Enhanced dashboard metrics |
| 2.0.0 | 2024-01-30 | **Migrated to Dependabot-only pipeline, removed OWASP Dependency Check** |

---

*This security pipeline is designed to provide comprehensive, automated security monitoring for the WebGoat project using GitHub's native Dependabot capabilities while maintaining development velocity and ensuring compliance with security best practices.* 