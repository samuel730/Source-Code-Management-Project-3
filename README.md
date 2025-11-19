# Source-Code-Management-Project-3

Below is a **clean, professional, fully organized documentation** version of the entire Critical Thinking Project. You can submit this as your final report or present it to your team. If you want it exported as **PDF, DOCX, or Markdown**, just tell me.

---

# **Source Code Management for a Distributed Development Team**

### **Critical Thinking Project Documentation**

---

## **Table of Contents**

1. **Introduction**
2. **Task 1: Evaluation of SCM Tools**

   * Centralized vs Distributed VCS
   * Advantages of Git
   * Challenges & Recommendation
3. **Task 2: Git Workflow for a Distributed Team**

   * Branching Strategy
   * Pull Requests & Reviews
   * Merging & Rebasing Best Practices
4. **Task 3: CI/CD Integration Using GitHub Actions**

   * Pipeline Overview
   * Example Workflow File
5. **Task 4: Security Best Practices in Git**

   * Access Control
   * Commit Signing
   * Branch Protection
6. **Task 5: Real-World Merge Conflict Resolution**

   * Steps to Resolve
   * Prevention Strategies
7. **Conclusion**

---

# **1. Introduction**

As the development team transitions from a centralized environment into a distributed one, the need for a scalable and modern Source Code Management (SCM) solution becomes critical. This documentation outlines an evaluation of SCM tools, implements a Git-based workflow, integrates CI/CD automation, enforces repository security, and addresses real-world challenges such as merge conflicts. The goal is to ensure that the distributed team can collaborate efficiently, securely, and with consistent code quality.

---

# **2. Task 1: Evaluation of SCM Tools**

## **Centralized vs Distributed Version Control**

### **Centralized VCS (e.g., SVN)**

* A single, central repository where all developers push and pull code.
* Requires a stable network connection for most operations.
* Simpler to understand but introduces bottlenecks and a single point of failure.

### **Distributed VCS (e.g., Git)**

* Every developer has a complete local copy of the repository.
* Offline-friendly: commits, diffs, and logs run locally.
* Flexible, fast branching and merging.
* Compatible with modern automation and cloud workflows.

---

## **Advantages of Git for a Distributed Team**

* Faster local operations improve productivity.
* Branching and merging are efficient, enabling feature-branch workflows.
* No single point of failure—every clone is a full backup.
* Strong ecosystem: GitHub, GitLab, Bitbucket, CI/CD, DevSecOps tooling.

---

## **Challenges of Using Git**

* Steeper learning curve than SVN.
* Requires workflow discipline (e.g., understanding rebase and branching).
* Large binary files need Git LFS.

---

## **Recommendation**

Git is the best choice for distributed teams due to its speed, flexibility, resilience, and compatibility with modern CI/CD and DevOps practices. Migrating from SVN to Git will significantly improve collaboration and automation capabilities across geographically distributed developers.

---

# **3. Task 2: Git Workflow for a Distributed Team**

## **Branching Strategy**

A hybrid workflow is recommended:

### **Main Branches**

* **`main`** – Always stable, production-ready. Protected.
* **`develop`** (optional) – Integration branch for upcoming releases.

### **Supporting Branches**

* **`feature/<name>`** – One feature per branch. Short-lived.
* **`hotfix/<issue>`** – Urgent production fixes.
* **`release/<version>`** – Used if following formal releases.

---

## **Pull Requests (PRs) and Code Reviews**

Each feature branch must go through the following:

1. Push the feature branch to the remote.
2. Open a **Pull Request** into `main` (or `develop`).
3. PR must include:

   * Summary of changes
   * Linked issue/ticket
   * Testing instructions
4. The PR should pass:

   * Reviewer approval
   * CI tests (linting, unit tests, build)
5. Merge into target branch only when CI and reviews are complete.

---

## **Best Practices for Merging & Rebasing**

* Keep branches small and short-lived.
* Rebase feature branches frequently to reduce conflicts:

  ```
  git fetch origin
  git rebase origin/main
  ```
* Use **force-with-lease** instead of force push:

  ```
  git push --force-with-lease
  ```
* Avoid rebasing shared branches after others have pulled them.

---

# **4. Task 3: Automating Code Quality and Deployment**

## **CI/CD Pipeline Overview**

Using GitHub Actions, enforce automated testing and deployment:

* When developers push or open a PR:

  * Linting runs
  * Unit tests execute
  * Test results are stored
* When code merges into `main`:

  * Build process starts
  * Application is containerized
  * Deployed to a staging environment

---

## **Example GitHub Actions Workflow**

Create a file:

### **`.github/workflows/ci-cd.yml`**

```yaml
name: CI / CD

on:
  push:
    branches: [ main, develop, 'release/*' ]
  pull_request:
    branches: [ main, develop ]

jobs:
  lint-and-test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Unit tests
        run: npm test -- --ci --reporter=jest-junit

      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: ./test-results

  build-and-staging-deploy:
    needs: lint-and-test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main' || startsWith(github.ref, 'refs/heads/release/')
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install
        run: npm ci

      - name: Build
        run: npm run build

      - name: Create Docker image
        run: docker build -t myapp:${{ github.sha }} .

      - name: Push image to registry
        run: |
          echo "${{ secrets.REGISTRY_PASSWORD }}" | docker login ghcr.io -u "${{ secrets.REGISTRY_USERNAME }}" --password-stdin
          docker tag myapp:${{ github.sha }} ghcr.io/${{ github.repository_owner }}/myapp:${{ github.sha }}
          docker push ghcr.io/${{ github.repository_owner }}/myapp:${{ github.sha }}

      - name: Deploy to staging
        uses: appleboy/ssh-action@v0.1.7
        with:
          host: ${{ secrets.STAGING_HOST }}
          username: ${{ secrets.STAGING_USER }}
          key: ${{ secrets.STAGING_SSH_KEY }}
          script: |
            docker pull ghcr.io/${{ github.repository_owner }}/myapp:${{ github.sha }}
            docker stop myapp || true
            docker rm myapp || true
            docker run -d --name myapp -p 80:80 ghcr.io/${{ github.repository_owner }}/myapp:${{ github.sha }}
```

---

# **5. Task 4: Security Best Practices for Distributed Teams**

## **Access Controls**

* Enforce role-based access (RBAC):

  * Developers → feature branches only
  * Maintainers → merge permissions
* Use SSO or MFA for all accounts.

---

## **SSH Keys and Secure Authentication**

* Require SSH keys for all developers.
* Rotate keys regularly.
* Use GitHub/GitLab secrets for CI authentication.

---

## **Branch Protection**

Protect `main` and release branches by enabling:

* Required PR approvals
* Required CI checks
* No direct pushes
* No force pushes
* Require branches to be up-to-date before merging

---

## **Commit Signing**

Require cryptographically signed commits for traceability:

```
git config --global user.signingkey <KEYID>
git config --global commit.gpgsign true
```

This ensures authenticity and avoids unauthorized changes.

---

## **Audit Logging**

* Use platform audit logs to monitor:

  * Permission changes
  * CI/CD deployments
  * Security events
* Enable secret scanning and vulnerability detection.

---

# **6. Task 5: Real-World Git Challenge (Merge Conflict Resolution)**

## **Scenario**

Two developers accidentally introduced conflicting changes into `main`. The merge failed, and the repo is in a conflicted state.

---

## **Steps to Resolve Conflicts**

### **1. Create a Fix Branch**

```
git fetch origin
git checkout main
git pull origin main
git checkout -b fix/merge-conflict
```

### **2. Identify Conflicting Files**

```
git status
git diff --name-only --diff-filter=U
```

### **3. Open Files and Manually Resolve**

Look for:

```
<<<<<<< HEAD
Code from main
=======
Code from conflicting branch
>>>>>>> other
```

Edit and remove markers.

### **4. Mark Resolved & Commit**

```
git add .
git commit -m "Resolve merge conflict between branches"
git push origin fix/merge-conflict
```

Open a Pull Request for review.



## **Preventing Future Conflicts**

* Use small, frequent PRs.
* Communicate when editing shared files.
* Rebase often to pull in latest changes.
* Require PRs to be up-to-date before merging.
* Introduce feature flags to merge early and safely.



# **7. Conclusion**

Transitioning from a centralized SCM like SVN to a distributed solution such as Git empowers development teams to collaborate more effectively across different geographical locations. With proper branching strategies, CI/CD automation, security enforcement, and conflict-handling procedures, Git becomes a powerful tool that enhances productivity, code reliability, and software delivery speed.

This documentation ensures the team has the necessary guidelines and best practices to manage code efficiently and securely in a distributed DevOps environment.

