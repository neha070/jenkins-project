
# Jenkins CI/CD Pipeline for Code Quality and Security Analysis

## Overview

This repository contains a Jenkins pipeline designed to automate the process of assessing code quality, cyclomatic complexity, code coverage, and security vulnerabilities. The pipeline integrates various open-source tools to ensure high-quality standards in software development.

---

## Features

- **Automated Code Quality Analysis**: Integrates SonarQube for code quality checks and technical debt analysis.
- **Cyclomatic Complexity Evaluation**: Uses Lizard to calculate and report code complexity.
- **Code Coverage Reports**: Generates test coverage metrics using JaCoCo.
- **Security Vulnerability Scanning**: Implements Trivy and OWASP Dependency-Check for identifying vulnerabilities.
- **Continuous Integration**: Triggered on every commit to the `main` branch.


---

## Pipeline Workflow

The Jenkins pipeline includes the following stages:

1. **Checkout**: Cleans the workspace and fetches the latest code from the Git repository.
2. **Build**: Compiles and packages the code using Maven.
3. **Cyclomatic Complexity**: Analyzes the codebase using Lizard and generates a complexity report.
4. **Code Quality Check**: Analyzes the code with SonarQube and enforces quality gates.
5. **Quality Gate**: Ensures builds fail if quality gates are not met.
6. **Code Coverage**: Evaluates test coverage using JaCoCo and publishes the report.
7. **Security Scan**: Scans for vulnerabilities using Trivy and OWASP Dependency-Check.

---

## Tools Used

- **Jenkins**: For CI/CD automation.
- **Git**: For source code version control.
- **Maven**: For building and packaging Java applications.
- **SonarQube**: For code quality analysis.
- **JaCoCo**: For code coverage reporting.
- **Lizard**: For cyclomatic complexity analysis.
- **Trivy**: For vulnerability scanning.

---

## Setup Instructions

### Prerequisites

1. Install and configure Jenkins.
2. Set up the following tools:
   - **Maven**: [Install](https://maven.apache.org/install.html).
   - **SonarQube**: Set up a SonarQube server and generate a project token.
   - **Lizard**: Install using pip: `pip install lizard`.
   - **Trivy**: [Install](https://aquasecurity.github.io/trivy/v0.25.0/installation/).
  

### Steps

1. Clone the repository:
   ```bash
   git clone https://github.com/neha070/jenkins-project.git
   cd jenkins-project
   ```

2. Configure Jenkins:
   - Install required plugins: SonarQube Scanner, JaCoCo, HTML Publisher.
   - Add SonarQube token under **Manage Jenkins > Credentials**.
   - Set up Maven as a global tool in Jenkins.

3. Update the `Jenkinsfile`:
   - Replace placeholders with actual values for SonarQube token, server URL, and file paths.

4. Add the pipeline in Jenkins:
   - Create a new pipeline job.
   - Select "Pipeline script from SCM" and link to this repository.

---

## Reports and Outputs

- **Cyclomatic Complexity Report**: `complexity-report.txt`.
- **Code Quality Metrics**: Available on the SonarQube dashboard.
- **Code Coverage**: Detailed test coverage metrics from JaCoCo.
- **Security Vulnerability Reports**:
  - JSON: `trivy-report.json`
  - HTML: `trivy-report.html` (viewable via Jenkins).

---

## Troubleshooting

| **Issue**                      | **Cause**                                     | **Solution**                                    |
|--------------------------------|-----------------------------------------------|------------------------------------------------|
| `SonarQube token error`        | Invalid or expired token.                     | Generate a new token and update Jenkins.       |
| `Maven command not found`      | Maven not installed or configured in Jenkins. | Install Maven and configure Jenkins tools.     |
| `Trivy scan fails`             | Incorrect configuration or network issues.    | Verify Trivy installation and network access.  |
| `Quality Gate Timeout`         | Analysis taking too long.                     | Increase the timeout in the `Quality Gate` stage. |

---

## Contributors

- **Neha**: [GitHub Profile](https://github.com/neha070)

---
