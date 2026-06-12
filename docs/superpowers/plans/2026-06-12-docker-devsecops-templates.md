# Docker DevSecOps Templates Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a GitHub-ready repository with optimized, secure Docker templates for Java Spring Boot, Angular and React.

**Architecture:** Each technology has an isolated template folder with a multi-stage Dockerfile and supporting runtime config. Repository-level documentation and CI describe and enforce DevSecOps practices.

**Tech Stack:** Docker, Maven, Eclipse Temurin, Node.js, Nginx, GitHub Actions, Hadolint, Trivy.

---

### Task 1: Repository Documentation

**Files:**
- Create: `README.md`
- Create: `SECURITY.md`
- Create: `LICENSE`

- [ ] **Step 1: Write README**

Create a French README explaining repository purpose, folder structure, build commands, security choices, Trivy usage, secrets handling, image signing and registry recommendations.

- [ ] **Step 2: Add security policy**

Create `SECURITY.md` with vulnerability reporting guidance and supported template scope.

- [ ] **Step 3: Add MIT license**

Create `LICENSE` with MIT license text.

### Task 2: Java Spring Boot Template

**Files:**
- Create: `java-spring-boot/Dockerfile`
- Create: `java-spring-boot/.dockerignore`

- [ ] **Step 1: Add multi-stage Dockerfile**

Use Maven with Eclipse Temurin 17 for build, cache Maven dependencies, package with tests skipped by default through an ARG, and run on a JRE Alpine image as a non-root user.

- [ ] **Step 2: Add Docker ignore**

Exclude VCS metadata, target folders, IDE files, logs and local environment files.

### Task 3: Angular Template

**Files:**
- Create: `angular/Dockerfile`
- Create: `angular/.dockerignore`
- Create: `angular/nginx.conf`

- [ ] **Step 1: Add Angular Dockerfile**

Use Node 20 Alpine for build, `npm ci`, configurable Angular output path, then copy static assets into Nginx.

- [ ] **Step 2: Add hardened Nginx config**

Serve SPA routes with `try_files`, expose health endpoint, add security headers and run on port 8080 for non-root Nginx.

- [ ] **Step 3: Add Docker ignore**

Exclude dependencies, build output, caches, logs and local env files.

### Task 4: React Template

**Files:**
- Create: `react/Dockerfile`
- Create: `react/.dockerignore`
- Create: `react/nginx.conf`

- [ ] **Step 1: Add React Dockerfile**

Use Node 20 Alpine for build, `npm ci`, configurable build output path, then copy static assets into Nginx.

- [ ] **Step 2: Add hardened Nginx config**

Serve SPA routes with `try_files`, expose health endpoint, add security headers and run on port 8080 for non-root Nginx.

- [ ] **Step 3: Add Docker ignore**

Exclude dependencies, build output, caches, logs and local env files.

### Task 5: CI Security Workflow

**Files:**
- Create: `.github/workflows/security-scan.yml`

- [ ] **Step 1: Add Hadolint job**

Run Hadolint against all template Dockerfiles.

- [ ] **Step 2: Add Trivy config scan**

Run Trivy filesystem scan against the repository and fail on critical or high findings.

### Task 6: GitHub Publication

**Files:**
- Modify: Git repository metadata

- [ ] **Step 1: Verify repository status**

Run `git -c safe.directory="C:/Users/jia-g/Documents/DevOps toolkit" status --short`.

- [ ] **Step 2: Commit files**

Stage and commit the template repository.

- [ ] **Step 3: Create GitHub repository**

Use `gh repo create` if authenticated, then push the default branch.

## Self-Review

- Spec coverage: Dockerfiles, README, DevSecOps rules, Trivy and GitHub publication are covered.
- Placeholder scan: no TBD, TODO, or vague implementation-only placeholders remain.
- Scope check: one focused template repository; no decomposition needed.
