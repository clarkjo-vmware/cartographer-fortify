# Cartographer Fortify Integration

This repository demonstrates how to integrate **Fortify** static application security testing (SAST) into a **Tanzu Application Platform (TAP) Supply Chain** using **Cartographer**. Fortify helps ensure that your applications are scanned for security vulnerabilities during the build process, providing critical insights into potential risks early in the development lifecycle.

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Configuration](#configuration)
- [Fortify Integration](#fortify-integration)
- [Contributing](#contributing)
- [License](#license)

## Overview

The **Cartographer Fortify Integration** enables security scanning as part of a TAP supply chain. By adding Fortify as a stage in your Cartographer supply chain, you can automatically scan source code for security vulnerabilities as part of the CI/CD process. This allows you to catch security issues early, enhancing the security posture of your applications.

This repository provides resources such as supply chain definitions, templates, and configuration files for integrating Fortify with TAP.

## Prerequisites

Before using this repository, ensure that the following tools and services are set up and accessible:

- **Tanzu Application Platform (TAP)**: Install TAP and configure it to work with your Kubernetes cluster.
- **Cartographer**: Ensure that Cartographer is installed and working in your environment.
- **Fortify Static Code Analyzer (SCA)**: Access to Fortify, including credentials for authentication.
- **Kubernetes Cluster**: A working Kubernetes cluster where the supply chain will be executed.
- **Docker/OCI Image Registry**: An accessible registry for storing container images.

## Installation

### 1. Clone the Repository

Begin by cloning this repository to your local development environment:

```bash
git clone https://github.com/clarkjo-vmware/cartographer-fortify.git
cd cartographer-fortify
```

### 2. Apply Kubernetes Manifests

Next, apply the Kubernetes resources required to set up the Cartographer supply chain:

```bash
kubectl apply -f supplychain.yaml
kubectl apply -f fortify-template.yaml
```

These files configure the supply chain and add Fortify as a step for scanning application code.

### 3. Set Up Fortify Credentials

You need to provide Fortify credentials so that the supply chain can authenticate with the Fortify server. Store the credentials in a Kubernetes secret:

```bash
kubectl create secret generic fortify-credentials \
  --from-literal=api-key=<your-fortify-api-key> \
  --namespace <your-namespace>
```

Ensure that this secret is referenced correctly in the supply chain.

## Usage

Once the supply chain is installed and configured, you can trigger it by creating a workload. Use the `tanzu` CLI or Kubernetes directly to create the workload:

```bash
tanzu apps workload create <workload-name> \
  --git-repo https://github.com/<your-repo> \
  --git-branch main \
  --type web
```

This will initiate the supply chain, which will include steps to build the application, run Fortify scans, and deploy the application to the Kubernetes cluster.

### Monitoring the Supply Chain

To monitor the progress of the supply chain, use the following command:

```bash
tanzu apps workload get <workload-name>
```

The supply chain will fail and halt if the Fortify scan detects vulnerabilities that exceed the predefined threshold.

## Configuration

### Supply Chain Configuration

The supply chain is configured via the `supplychain.yaml` file. You can customize the pipeline by editing this file to include additional stages or change the behavior of existing ones.

The Fortify scan is included as a custom resource in the supply chain definition. You can modify settings such as scan thresholds, server URL, and scan depth in the `fortify-template.yaml`.

## Fortify Integration

The Fortify integration is implemented as a custom template in Cartographer. The Fortify step scans the application’s source code for security vulnerabilities and reports any issues found.

The scan results will determine whether the pipeline continues to the next stage or stops if the vulnerabilities exceed configured thresholds. You can customize the behavior of Fortify by adjusting the `fortify-template.yaml` file.

### Key Fortify Features:
- **Vulnerability Scanning**: Scans for common vulnerabilities like SQL injection, XSS, buffer overflows, etc.
- **Configurable Thresholds**: Set thresholds for vulnerability severity (e.g., fail on "High" or above).
- **Comprehensive Reports**: Results are available in the Fortify dashboard or through APIs.
