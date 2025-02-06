
---

# CI/CD and Build Security Cheat Sheet

---

## I. Introduction

- **Purpose:**  
  - **Protect** the software development process from vulnerabilities and threats.
- **Context:**  
  - Recent attacks such as the SolarWinds incident (2020) emphasize the need for robust security in build pipelines.

---

## II. Fundamentals of CI/CD

- **Single Source Repository:**  
  - **Definition:** Centralized storage for code, scripts, configuration files, and build artifacts.  
  - **Example:** Using GitLab or GitHub to maintain a unified repository.
  
- **Frequent Check-ins to the Main Branch:**  
  - **Concept:** Regular, small commits help in seamless integration.  
  - **Benefits:** Reduces merge conflicts and speeds up the integration process.
  
- **Automated Builds:**  
  - **Function:** Builds are automatically triggered when changes are pushed.
  
- **Self-Testing Builds:**  
  - **Key Idea:** Integrate automated tests (unit, integration, security tests) into the build process.  
  - **Benefits:** Ensures code quality and immediate detection of issues.
  
- **Frequent Iterations:**  
  - **Practice:** Commit small, incremental updates to help identify and resolve conflicts quickly.
  
- **Stable Testing Environments:**  
  - **Definition:** Environments that closely mimic production for accurate performance testing.  
  - **Example:** A staging environment that replicates production settings.
  
- **Maximum Visibility:**  
  - **Goal:** Ensure all team members have access to the latest builds and code changes.
  
- **Predictable Deployments Anytime:**  
  - **Objective:** Streamline the pipeline to allow rapid, reliable deployments without compromising stability.

---

## III. Securing the Build Source (Source Code)

- **Common Main Issues:**  
  - **Unauthorized Tampering:** Ensure only authorized users can push new code.  
  - **Unauthorized Disclosure:**  
    - **Risk:** Accidental exposure of sensitive or confidential information.  
    - **Example:** Misconfigured repositories (like a public GitLab instance) leading to exposure of internal code.
  
- **Security Measures for the Build Source:**  
  - **Granular Access Control:**  
    - **Group-Based Access:** Organize projects into groups and apply uniform rules.
  - **Defined Access Levels:**  
    - **Levels:** Guest, Reporter, Developer, Maintainer, Owner.  
    - **Practice:** Assign the minimum necessary permissions to reduce risk.
  - **Sensitive Information Protection:**  
    - **.gitignore:** Use to exclude sensitive files (e.g., passwords, API keys).  
    - **Environment Variables:** Store sensitive data separately from the source code.
  - **Branch Protection:**  
    - **Purpose:** Enforce code reviews and prevent unauthorized direct pushes.
  - **Ongoing Best Practices:**  
    - Regular audits and updates of access permissions.  
    - Enforcement of **two-factor authentication (2FA)**.  
    - Continuous monitoring of audit logs and repository scans.

---

## IV. Securing the Build Process

- **Goals:**  
  - Protect the process from misconfigurations that could lead to vulnerabilities such as supply chain attacks, remote code execution, or dependency injection.
  
- **Key Focus Areas:**

  - **Securing Dependencies:**  
    - **Concern:** External libraries and SDKs can be targets for supply chain attacks or dependency confusion.
  
  - **Managing Build Triggers:**  
    - **What Actions Start the Build Process?**  
      - Define specific triggers (for example, only commits to the main branch).  
      - **Balance:** Between extensive testing and minimizing attack surface.
    - **Who Can Start the Build Process?**  
      - Restrict build triggers to trusted users, e.g., those with merge approval rights.
    - **Where will the Build Occur?**  
      - Use segregated build agents or environments based on build sensitivity.  
      - **Example:** Separate runners for experimental branches and production code.
  
- **Attack Scenario – Exploiting a Merge Build:**  
  - **Vulnerability:**  
    - An attacker modifies a pipeline file (e.g., Jenkinsfile) if it can be altered by any user.
  - **Attack Process:**  
    1. **Fork the Repository:** The attacker creates a private fork.
    2. **Modify the Jenkinsfile:** Change commands to include malicious code such as a reverse shell script.
    3. **Create a Merge Request:** This triggers the malicious commands on the build agent.
  - **Real-Life Example:**  
    - An attacker replaces a Jenkinsfile command with one that downloads and executes a remote shell, resulting in a compromised build agent.
  
- **Best Practices to Harden the Build Process:**  
  - **Isolation and Containerisation:** Run each build in an isolated container.  
  - **Least Privilege:** Grant minimal permissions to CI/CD tools and build agents.  
  - **Secret Management:** Use CI/CD tools’ secret management to store sensitive data securely.  
  - **Immutable Artifacts:** Securely store output in registries that allow auditing.  
  - **Dependency Scanning:** Regularly scan third-party libraries for vulnerabilities.  
  - **Pipeline as Code:** Maintain version-controlled pipeline definitions for traceability.  
  - **Regular Updates:** Keep build tools and dependencies up-to-date.  
  - **Logging and Monitoring:** Implement robust systems to detect unusual build behavior.

---

## V. Securing the Build Server

- **Importance:**  
  - The build server is a critical component; its compromise can affect every stage of the software supply chain.
  
- **Build Server Basics:**  
  - **Access Control:**  
    - **Granular Access:** Limit who can log into and control the build server.  
    - **Avoid Default Credentials:** Do not use simple defaults like jenkins:jenkins.
  - **Multi-Factor Authentication (MFA):**  
    - Enforce MFA to reduce risks associated with stolen or weak credentials.
  
- **Operational Practices:**  
  - **Regular Updates:** Patch the server and build agents to mitigate known vulnerabilities.
  - **Network Isolation:** Limit network traffic to only essential services using VPNs and firewalls.
  
- **Securing Build Agents and Servers:**  
  - **Restricted Communication:**  
    - Ensure agents communicate only with the central build server.
  - **Network-Level Security:**  
    - Place build servers/agents in private segments, configure firewalls, and utilize VPNs.
  - **Authentication Enhancements:**  
    - **Token-Based Authentication:** Use tokens for authenticated communications between build agents and servers.
  - **Continuous Protection:**  
    - **Monitoring & Logging:** Track activity to spot unusual patterns.  
    - **Security Audits:** Regularly review the server configuration to spot potential vulnerabilities.
  - **Real-World Example:**  
    - A Jenkins server left with default credentials was exploited via the script console.  
    - **Takeaway:** Always change default settings and enforce strict access controls.

---

## VI. Securing the Build Pipeline

- **Objective:**  
  - Protect the end-to-end CI/CD pipeline from insider threats and compromised developer accounts.
  
- **Access Gates (Checkpoints):**  
  - **Definition:** Stages where code must pass predetermined quality and security checks before progressing.
  - **Benefits:**  
    - **Enhanced Control:** Regulates code progression.  
    - **Quality Assurance:** Confirms that code meets testing and review criteria.  
    - **Security Checks:** Integrates automated vulnerability scans.
  
- **Implementation Steps:**  
  - **Manual Approvals:**  
    - Require human reviews at critical stages.  
    - **Example:** Merge requests must be approved by a team member other than the author.
  - **Automated Tests & Security Scans:**  
    - Execute tests alongside code quality checks whenever changes occur.
  - **Release Gates and Environment Validation:**  
    - Verify that documentation, versioning, and compliance measures are in place.
  - **Rollback Plan and Monitoring:**  
    - Have procedures for rollback and continuously monitor post-deployment performance.
  - **Parallel and Audited Gates:**  
    - Run checks concurrently and review outcomes regularly.
  
- **The Two-Person Concept:**  
  - **Definition:** A security measure ensuring that one person cannot unilaterally approve and pass code through the pipeline.  
  - **Practice:** Enforce separation of duties by requiring multiple approvals.
  
- **Handling Misconfigured Access Gates:**  
  - **Scenario:**  
    - An attacker using a developer account may exploit a misconfigured gate that permits self-approval.  
  - **Lesson Learned:**  
    - Ensure technical controls enforce policies (e.g., mandatory multi-approvals).
  
- **Protection Strategies:**  
  - **Limit Branch Access:**  
    - Restrict push permissions on sensitive branches (e.g., main/master).
  - **Review Merge Requests:**  
    - Always require multiple reviews for sensitive code changes.
  - **CI/CD Variables:**  
    - Store secrets outside source code to avoid direct exposure.
  - **Limit Runner Access:**  
    - Use runner tags and restrict which runners can execute jobs for protected branches.
  - **Regular Audits and Monitoring:**  
    - Conduct periodic reviews of pipeline configurations and access controls.

---

## VII. Securing the Build Environment

- **Overview:**  
  - **Objective:** Protect the build and deployment pipeline from threats that may impact the production environment.
  - **Key Focus:** Prevent misconfigurations that allow a compromised environment to affect production builds.
  
- **Environment Segregation:**  
  - **Definition:** Separating pipeline environments (e.g., DEV, PREPROD, PROD) to ensure that misconfigurations in one do not impact another.
  - **Best Practice:**  
    - **Tighter Controls in PROD:** Production environments should have stricter access than development.
  - **Agile Consideration:**  
    - Developers may have broader access in DEV environments increasing risks.
  
- **Common Misconfiguration:**  
  - **Shared Build Agent:**  
    - Using a single runner for both DEV and PROD builds can lead to cross-contamination.
  
- **Best Practices for Environment Security:**
  
  - **Isolate Environments:**  
    - **Separate Runners:**  
      - Assign distinct runners (e.g., "DevRunner" for DEV, "ProdRunner" for PROD) to prevent a compromise in one from affecting the other.
  
  - **Restrict Access for CI/CD Jobs:**  
    - Limit access to modify CI/CD configuration files to only authorized personnel.
  
  - **Implement Monitoring and Alerting:**  
    - **Real-Time Monitoring:** Set up alerts for unusual job executions or unauthorized changes.  
    - **Regular Auditing:** Periodically review access permissions and build logs.
  
  - **Real-Life Example:**  
    - In one case, a shared runner between DEV and PROD allowed a threat actor with DEV access to compromise production builds, emphasizing the need for environment segregation.

---

These notes provide an overview of best practices and considerations for securing CI/CD pipelines and build environments, ensuring a comprehensive foundation in both process and technical controls.
