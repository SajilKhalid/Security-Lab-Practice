# Perform System Configuration Gap Analysis

## Objective

The objective of this lab was to perform a security configuration gap analysis by comparing a Windows Server system's current configuration against a Microsoft security baseline template. This lab demonstrated how security teams identify configuration deviations, evaluate compliance, and improve an organization's security posture.

## Environment

- Organization Scenario: Structureality Inc.
- Virtual Machine: PC10
- Operating System: Windows Server 2019 (Version 1809, OS Build 17763)
- Tool Used: Microsoft Policy Analyzer
- Security Baseline: Microsoft Security Compliance Toolkit

## Lab Overview

In this lab, I used Microsoft Policy Analyzer to compare a Windows Server 2019 system against a predefined security baseline template. The goal was to identify differences between the expected security configuration and the system's current effective state.

This process is known as gap analysis and is commonly used to evaluate security compliance, identify misconfigurations, and support security hardening efforts.

## Steps Performed

1. Verified the Windows Server version and OS build using the `winver` command.
2. Opened PowerShell with administrative privileges.
3. Extracted the Microsoft Policy Analyzer tool and Windows Server 2019 security baseline files.
4. Loaded the Microsoft security baseline template into Policy Analyzer.
5. Reviewed baseline security settings using the View/Compare feature.
6. Compared the security baseline against the system's effective configuration.
7. Analyzed differences between the expected baseline values and current system settings.

## Results

The gap analysis identified configuration differences between the Microsoft security baseline and the current PC10 system configuration.

Examples of identified differences:

| Policy Setting | Baseline Value | Effective State |
|---|---:|---:|
| LockoutBadCount | 10 | 0 |
| MinimumPasswordLength | 14 | 7 |

Based on the comparison results, the PC10 system was **not compliant** with the security template because several configuration values differed from the recommended baseline.

## Screenshots

### Windows Server Version Verification
![Windows Version](./images/Windows%20Version.png)

### Policy Analyzer Baseline Loaded
![Policy Analyzer Baseline](./images/Compare%20policies.png)

### Policy Comparison Results
![Policy Comparison](./images/Analyzing%20the%20difference%20between%20the%20effective%20state%20and%20the%20baseline.png)

### Gap Analysis Differences
![Gap Analysis Results](./images/Analyzing%20the%20difference%20between%20the%20effective%20state%20and%20the%20baseline.png)

## Key Takeaways

- Security baselines provide recommended configurations to help organizations maintain a secure environment.
- Gap analysis identifies differences between a desired security state and the current system state.
- Configuration compliance checks help organizations discover security weaknesses before they become vulnerabilities.
- Security templates should be reviewed and customized based on an organization's specific risk profile and business requirements.
- Microsoft Policy Analyzer can be used to compare security policies and identify configuration gaps.