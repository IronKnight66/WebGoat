# 📚 Confluence Import Guide

This guide explains how to import the Security Pipeline Workflow Designs into Confluence.

## 📁 Files Created

- **`Security-Pipeline-Workflow-Designs.md`** - Complete workflow documentation with Mermaid diagrams
- **`Confluence-Import-Guide.md`** - This import guide

## 🔄 Import Methods

### **Method 1: Direct Copy-Paste (Recommended)**
1. Open `Security-Pipeline-Workflow-Designs.md` in a text editor
2. Copy the entire contents
3. In Confluence:
   - Create a new page
   - Switch to "Markup" view (or "Source" view)
   - Paste the content
   - Switch back to "Rich Text" view

### **Method 2: Import as Attachment**
1. In Confluence, go to your target space
2. Create a new page
3. Use "Insert" → "Files & images" → "Upload file"
4. Upload the `.md` file as an attachment
5. Link to it from your page content

### **Method 3: Mermaid Diagrams**
Confluence supports Mermaid diagrams using the Mermaid macro:

1. In your Confluence page, add a Mermaid macro
2. Copy the Mermaid code from between the triple backticks
3. Paste into the macro editor

Example:
```
{mermaid}
graph TD
    A[PR Opened] --> B[Checkout Code]
    B --> C[Setup JDK 23]
    ...
{mermaid}
```

## 📊 What's Included

### **Complete Documentation**
- ✅ 4 detailed workflow designs with Mermaid diagrams
- ✅ Pipeline architecture overview
- ✅ Data flow and integration patterns
- ✅ Authentication and permissions guide
- ✅ Error handling strategies
- ✅ Troubleshooting section

### **Mermaid Diagrams** 
- ✅ Feature Branch Security Scan workflow
- ✅ Main Branch Security Pipeline workflow  
- ✅ Security Rescan Scheduler workflow
- ✅ Security Metrics Dashboard workflow
- ✅ Overall Pipeline Architecture
- ✅ Data Flow Integration diagram

## 🎨 Confluence Formatting Tips

### **For Best Results:**
- Use Confluence's built-in Mermaid macro for diagrams
- Apply consistent heading styles
- Use info/warning macros for callout boxes
- Create a table of contents macro for navigation

### **Suggested Confluence Macros:**
- **Mermaid** - For workflow diagrams
- **Code Block** - For JSON examples and configurations
- **Info** - For key features and benefits  
- **Warning** - For troubleshooting and error scenarios
- **Table of Contents** - For navigation

## 📋 Recommended Page Structure

Create these Confluence pages:

1. **WebGoat Security Pipeline Overview** (Main page)
2. **Feature Branch Security Scan** (Child page)
3. **Main Branch Security Pipeline** (Child page)  
4. **Security Rescan Scheduler** (Child page)
5. **Security Metrics Dashboard** (Child page)
6. **Authentication & Setup Guide** (Child page)
7. **Troubleshooting & Support** (Child page)

## 🚀 Quick Start

1. Create main page: "WebGoat Security Pipeline Documentation"
2. Copy content from `Security-Pipeline-Workflow-Designs.md`
3. Paste into Confluence page using Markup view
4. Add Mermaid macros for diagrams
5. Apply formatting and create child pages as needed
6. Share with your team!

---

**Need Help?** Contact the team that created these workflows for assistance with import or customization. 