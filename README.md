# Source-Code-Management-Project-3

---
Author: Oluwatunmise Adesanya

Institution: Pistis Tech Hub

Course: Source Code Management Project 3

Date: 20th, November 2025

## Abstract

This research report investigates the transition from a centralized source code management system (SVN) to a distributed version control system (Git) within a geographically dispersed software development team. The study evaluates existing SCM tools, proposes a structured Git workflow, integrates CI/CD automation for quality assurance, discusses essential security mechanisms for distributed collaboration, and resolves a real-world merge conflict. The report applies critical analysis to justify the transition, presenting both technical and organizational benefits. Recommendations are provided to support sustainable DevOps practices within distributed development environments.

Table of Contents

1. Introduction

2. Evaluation of SCM Tools
 2.1 Centralized vs Distributed Systems
 2.2 Comparative Strengths and Weaknesses
 2.3 Critical Evaluation & Recommendation

3. Git Workflow Implementation
 3.1 Branching Strategy
 3.2 Pull Request Workflow
 3.3 Best Practices for Distributed Teams

4. CI/CD Automation Using GitHub Actions
 4.1 Pipeline Structure and Logic
 4.2 Workflow Design
 4.3 Benefits of CI/CD in Distributed Teams

5. Security Best Practices for SCM
 5.1 Access Control
 5.2 Commit Signing & Branch Protection
 5.3 Auditing & Secret Management

6. Real-World Merge Conflict Resolution
 6.1 Conflict Scenario
 6.2 Conflict Resolution Method
 6.3 Prevention Strategies

7. Conclusion


1. Introduction

As digital transformation accelerates, organizations increasingly depend on distributed development teams working across geographic and temporal boundaries. This shift requires robust Source Code Management (SCM) strategies to ensure efficient collaboration, code integrity, and security. Historically, centralized tools such as Subversion (SVN) were widely used, but they struggled to meet the dynamic needs of modern distributed teams.

This report critically evaluates SCM options, develops a Git-based workflow, implements automated CI/CD pipelines, enforces security best practices, and examines conflict-resolution techniques. The goal is to create a sustainable and scalable SCM environment aligned with DevOps principles.

2. Evaluation of SCM Tools
2.1 Centralized vs Distributed Version Control Systems
Table 1

Comparison of Centralized and Distributed VCS

Feature	Centralized VCS (e.g., SVN)	Distributed VCS (e.g., Git)
Repository Model: Single central server, Full local copies
Offline Capability: very limited,	Full offline history
Branching & Merging	Slow, expensive	Fast, lightweight
Collaboration Model: Sequential, Parallel, & distributed
Failure Risk	High (single point)	Low (redundant copies)
2.2 Strengths and Weaknesses
Centralized Systems (SVN)

Strengths:

Straightforward for beginners

Strong central authority

Good for linear workstreams

Weaknesses:

Requires constant server access

Difficult merges

Limited scalability for distributed teams

Distributed Systems (Git)

Strengths:

Supports asynchronous, global collaboration

Efficient branching and merging

Strong ecosystem (GitHub, GitLab)

Integrity ensured through SHA-1 hashing

Weaknesses:

Steeper learning curve

Requires structured workflow discipline

2.3 Critical Evaluation & Recommendation

After a comparative assessment, Git emerges as the more suitable SCM tool for distributed teams. Its offline capabilities, flexible workflow design, integration with CI/CD systems, and high-speed operations provide significant advantages over SVN. For an organization transitioning into distributed development, Git offers scalability, efficiency, and improved collaboration.

Recommendation: Adopt Git as the primary SCM system and integrate it with automated DevOps workflows.

3. Git Workflow Implementation
3.1 Branching Strategy

A structured branching model ensures consistency and prevents errors. The recommended hybrid workflow includes:

Main — stable, production-ready

Develop — integration branch

feature/* — individual features

hotfix/* — urgent production patches

release/* — staging for upcoming releases

Figure 1: Workflow Diagram (Text Version)
feature/* → develop → main → production
              ↑         ↓
           release/*   hotfix/*

3.2 Pull Request Workflow

Pull requests (PRs) enable structured collaboration and centralized review.

PR Requirements:

The branch must be up to date.

Automated tests must pass.

Reviewer approval required

Code must follow organizational style guidelin.es

This ensures higher code quality and reduces integration issues.

3.3 Best Practices for Distributed Teams

Rebase frequently to minimize conflicts.

Keep feature branches short-lived.d

Communicate shared file edits ear.ly

Require descriptive commit messages. 

Enforce signed commits for accountability

4. CI/CD Automation Using GitHub Actions
4.1 Pipeline Structure
Stage	Description
Linting	Ensures coding standards
Testing	Validates functionality
Build	Compiles application
Deploy	Releases to staging environment
4.2 Workflow Logic (Simplified YAML)
name: CI/CD Pipeline
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install
      - run: npm test

  deploy:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm run build

4.3 Benefits for Distributed Teams

Ensures consistent quality across multiple time zones

Prevents broken code from entering production

Reduces manual testing effort

Provides clear visibility of software stability

5. Security Best Practices for SCM
5.1 Access Control

Implement role-based access (RBAC)

Require MFA or SSO

Restrict administrative privileges

5.2 Commit Signing & Branch Protection

Commit signing prevents impersonation.

git config --global user.signingkey <KEYID>
git config --global commit.gpgsign true


Branch protection ensures that:

No direct pushes occur on main.

Reviews and checks are mandatory.

CI tests must pass before merging.

5.3 Auditing & Secret Management

Enable repository audit logs.

Use encrypted secret storage in CI/CD.

Rotate SSH keys periodically.

Enable dependency scanning

6. Real-World Merge Conflict Resolution
6.1 Conflict Scenario

Two developers modified the same file on separate branches and merged into main, resulting in conflicting changes.

6.2 Steps for Conflict Resolution

Fetch the latest commit.

Create a conflict resolution branch.

git checkout main  
git pull  
git checkout -b fix/conflict  


Identify conflicting files:

git status  
git diff --name-only --diff-filter=U


Manually resolve conflict markers:

<<<<<<< HEAD
Current main code
=======
Incoming feature code
>>>>>>> feature-branch


Stage and commit the final clean version:

Git add.
git commit -m "Resolved merge conflict"

6.3 Prevention Strategies

Use smaller, more frequent PRs

Enforce communication of major file edits.

Require branches to be updated before merging

Encourage rebasing over long-lived branches.

7. Conclusion

This report concludes that Git provides a modern, scalable, and reliable solution for distributed software teams. By implementing structured workflows, CI/CD automation, security measures, and conflict-resolution strategies, organizations can significantly enhance collaboration, maintain code integrity, and support continuous delivery practices.

The recommended transitions and procedures prepare the team for long-term sustainability, productivity, and security in a distributed development environment.
