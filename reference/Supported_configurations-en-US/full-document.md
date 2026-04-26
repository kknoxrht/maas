---
title: "Red Hat AI 3: Supported Product and Hardware Configurations"
source: "reference/Red_Hat_AI-3-Supported_product_and_hardware_configurations-en-US.pdf"
conversion_date: "2026-04-24"
conversion_method: "claude-optimized"
product: "Red Hat AI"
version: "3"
document_type: "technical documentation"
topics:
  - hardware configurations
  - AI accelerators
  - NVIDIA GPUs
  - AMD GPUs
  - IBM Spyre
  - Google TPU
  - AWS Neuron
  - quantization formats
  - product compatibility
  - deployment environments
  - lifecycle policy
last_updated: "2026-03-23"
chunk_id: "supported-configurations-full"
total_chunks: 3
---

# Red Hat AI 3: Supported Product and Hardware Configurations

**Last Updated:** 2026-03-23

## Abstract

Learn about supported hardware and software configurations for Red Hat AI.

---

## Chapter 1: About Red Hat AI Inference Server Supported Hardware and Software

Red Hat AI software runs on a variety of supported hardware, software, and delivery platforms in production environments.

**Important:** Technology Preview and Developer Preview features are provided for early access to potential new features. Technology Preview or Developer Preview features are not supported or recommended for production workloads.

Supported configurations span multiple AI accelerator types including:
- **NVIDIA GPUs** (Turing, Ampere, Ada Lovelace, Hopper, Blackwell)
- **AMD GPUs** (Instinct MI210, MI300X, MI325X)
- **Google TPUs** (v4, v5e, v5p, v6e Trillium)
- **IBM Spyre accelerators** (Power, Z, x86)
- **AWS Neuron** (Inferentia2, Trainium)

Red Hat AI Inference Server can be deployed in:
- OpenShift Container Platform clusters
- Standalone Red Hat Enterprise Linux (RHEL) hosts with Podman
- Integrated with Red Hat OpenShift AI for managed AI/ML workflows

### Additional Resources

- [Red Hat AI documentation](https://access.redhat.com/documentation/en-us/red_hat_ai/)
- [Red Hat Enterprise Linux AI documentation](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_ai/)
- [Red Hat OpenShift AI documentation](https://access.redhat.com/documentation/en-us/red_hat_openshift_ai/)
- [Red Hat AI on Hugging Face](https://huggingface.co/redhat)

---

## Chapter 2: Product and Version Compatibility

The following tables list the supported product versions for Red Hat AI Inference Server, Red Hat Enterprise Linux AI, and Red Hat OpenShift AI.

### AI Inference Server Product and Version Compatibility

| Product version | vLLM core version | LLM Compressor version |
|-----------------|-------------------|------------------------|
| 3.4.0-ea.1 | v0.14.1 | v0.9.0.2 |
| 3.3 | v0.13.0 | v0.9.0.1 |
| 3.2.5 | v0.11.2 | v0.8.1 |
| 3.2.4 | v0.11.0 | v0.8.1 |
| 3.2.3 | v0.11.0 | v0.8.1 |
| 3.2.2 | v0.10.1.1 | v0.7.1 |
| 3.2.1 | v0.10.0 | Not included in this release |
| 3.2.0 | v0.9.2 | Not included in this release |

### Red Hat OpenShift AI Product and Version Compatibility

| Product version | vLLM core version | LLM Compressor version |
|-----------------|-------------------|------------------------|
| 3.3 | v0.13.0 | v0.9.0.1 |
| 3.2 | v0.11.2 | v0.8.1 |
| 3.0 | v0.11.0 | v0.8.1 |

### Red Hat Enterprise Linux AI Product and Version Compatibility

| Product version | vLLM core version | LLM Compressor version |
|-----------------|-------------------|------------------------|
| 3.3 | v0.13.0 | v0.9.0.1 |
| 3.2 | v0.11.2 | v0.8.1 |
| 3.0 | v0.11.0 | v0.8.1 |

---

## Chapter 3: Supported AI Accelerators for Red Hat AI Inference Server

The following tables list the supported AI data center grade accelerators for Red Hat AI Inference Server 3.4.

**Important:** Red Hat AI Inference Server supports data center grade AI accelerators only.

### Supported NVIDIA AI Accelerators

**Container image:** `registry.redhat.io/rhaii-early-access/vllm-cuda-rhel9:3.4.0-ea.1`

| vLLM release | AI accelerators | Requirements | vLLM architecture support | LLM Compressor support |
|--------------|-----------------|--------------|---------------------------|------------------------|
| v0.14.1 | **NVIDIA data center GPUs:**<br>• Turing: T4<br>• Ampere: A2, A10, A16, A30, A40, A100<br>• Ada Lovelace: L4, L20, L40, L40S<br>• Hopper: H100, H200, H20, GH200<br>• Blackwell: GB200, GB300, B200, B300, RTX PRO 6000 Blackwell Server Edition, RTX PRO 4500 Blackwell Server Edition | • CUDA Toolkit 13.0<br>• NVIDIA Container Toolkit 1.14<br>• NVIDIA GPU Operator 24.3<br>• Python 3.12<br>• PyTorch 2.9.1 | x86, AArch64 | Supported, now packaged separately in the model-opt-cuda-rhel9 container image |

**Important:** 
- Red Hat AI Inference Server 3.4.0-ea.1 is built with CUDA 13.0
- The container images are backward compatible with CUDA 12.9 drivers
- If your host driver version is older than the CUDA toolkit version shipped in the AI Inference Server container, you can use NVIDIA Forward Compatibility to avoid driver upgrades

**Note:** NVIDIA T4 and A100 accelerators do not support FP8 (W8A8) quantization.

### Supported AMD AI Accelerators

**Container image:** `registry.redhat.io/rhaii-early-access/vllm-rocm-rhel9:3.4.0-ea.1`

| vLLM release | AI accelerators | Requirements | vLLM architecture support | LLM Compressor support |
|--------------|-----------------|--------------|---------------------------|------------------------|
| v0.14.1 | • AMD Instinct MI210<br>• AMD Instinct MI300X<br>• AMD Instinct MI325X | • ROCm 6.3.4<br>• AMD GPU Operator 6.2<br>• Python 3.12<br>• PyTorch 2.9.1 | x86 | Not supported |

**Note:** AMD GPUs support FP8 (W8A8) and GGUF quantization schemes only.

### Supported Google TPU AI Accelerators (Technology Preview)

**Container image:** `registry.redhat.io/rhaii-early-access/vllm-tpu-rhel9:3.4.0-ea.1`

| vLLM release | AI accelerators | Requirements | vLLM architecture support | LLM Compressor support |
|--------------|-----------------|--------------|---------------------------|------------------------|
| v0.14.1 | Google v4, v5e, v5p, v6e (Trillium) | Python 3.12 | x86 Technology Preview | Not supported |

### Supported IBM Spyre AI Accelerators

**Container image:** `registry.redhat.io/rhaii-early-access/vllm-spyre-rhel9:3.4.0-ea.1`

#### IBM Spyre for Power (ppc64le)

| vLLM release | AI accelerators | Requirements | vLLM architecture support | LLM Compressor support |
|--------------|-----------------|--------------|---------------------------|------------------------|
| v0.14.1 | IBM Spyre for Power (ppc64le) | • Python 3.12.9<br>• PyTorch 2.7.1<br>• vllm-tgis-adapter 0.9.2<br>• vllm-spyre 1.6.1<br>• IBM Spyre Enablement Stack 1.1.1 | IBM Power (ppc64le) | Not supported |

#### IBM Spyre for Z (s390x)

| vLLM release | AI accelerators | Requirements | vLLM architecture support | LLM Compressor support |
|--------------|-----------------|--------------|---------------------------|------------------------|
| v0.14.1 | IBM Spyre for Z (s390x) | • Python 3.12<br>• PyTorch 2.7.1<br>• vllm-tgis-adapter 0.9.2<br>• vllm-spyre 1.2.0<br>• IBM Spyre Enablement Stack 1.1.1 | IBM Z (s390x) | Not supported |

#### IBM AIU (x86) - Technology Preview

| vLLM release | AI accelerators | Requirements | vLLM architecture support | LLM Compressor support |
|--------------|-----------------|--------------|---------------------------|------------------------|
| v0.14.1 | IBM AIU (x86) | • Python 3.12<br>• PyTorch 2.7.1<br>• vllm-tgis-adapter 0.9.2<br>• vllm-spyre 1.0.2<br>• IBM Spyre Enablement Stack 1.1.1 | x86 Technology Preview | Not supported |

**Important:** IBM AIU support for x86 is available as a **Technology Preview** feature only. IBM AIU for x86 is not a Generally Available (GA) feature. Technology Preview features are not supported with Red Hat production service level agreements (SLAs) and might not be functionally complete. Red Hat does not recommend using them in production. These features provide early access to upcoming product features, enabling customers to test functionality and provide feedback during the development process.

### Supported AWS Neuron AI Accelerators

**Container image:** `registry.redhat.io/rhaii-early-access/vllm-neuron-rhel9:3.4.0-ea.1`

| vLLM release | AI accelerators | Requirements | vLLM architecture support | LLM Compressor support |
|--------------|-----------------|--------------|---------------------------|------------------------|
| v0.14.1 | • AWS Inferentia2 (Inf2)<br>• AWS Trainium (Trn1, Trn1n, Trn2) | • AWS Neuron SDK 2.x<br>• Python 3.12<br>• vllm-neuron plugin | x86 Dev Preview | Not supported |

**Important:** AWS Trainium and Inferentia support is a **Technology Preview** feature only. Technology Preview features are not supported with Red Hat production service level agreements (SLAs) and might not be functionally complete. Red Hat does not recommend using them in production.

---

## Chapter 4: Supported AI Accelerator Model Quantization Formats

Different AI accelerator architectures support different types of model quantization, depending on the compute capabilities of the hardware. The following tables list the AI accelerators that support INT8, INT4, FP8, and NVFP4 quantization formats.

### Quantization Format Overview

- **INT8 (W8A8) quantization:** Reduces model weights and activations to 8-bit integers, providing significant memory savings while maintaining acceptable accuracy for many use cases

- **INT8 (W4A8) quantization:** Reduces model weights to 4-bit integers, keeping activations at 8-bit precision. Improves memory efficiency compared to W8A8 while preserving higher activation fidelity for inference

- **INT4 (W4A16) quantization:** Reduces model weights to 4-bit integers while maintaining 16-bit activations, enabling larger models to fit in GPU memory with minimal accuracy loss

- **FP8 (W8A8) quantization:** Uses 8-bit floating point representation for weights and activations, offering a balance between memory efficiency and numerical precision for training and inference workloads

- **NVFP4 quantization:** Uses NVIDIA's 4-bit floating point format with two-level scaling (FP8 fine-grained scales and FP32 tensor-level scale), providing maximum memory efficiency for inference on NVIDIA Blackwell hardware

### Supported NVIDIA AI Accelerators for INT8 (W8A8) Quantization

| Architecture | Supported AI accelerators | Minimum compute capability |
|--------------|---------------------------|----------------------------|
| Turing | Tesla T4 | 7.5 |
| Ampere | A10, A30, A40, A100 | 8.0 |
| Ada Lovelace | L4, L40, L40S | 8.9 |
| Hopper | H100, H200, GH200 | 9.0 |

**Note:** NVIDIA Blackwell architecture (B200, B300, GB200, GB300) does not support INT8 quantization in vLLM due to kernel limitations. Use FP8 or NVFP4 quantization instead.

### Supported AMD AI Accelerators for INT8 (W8A8) Quantization

| Architecture | Supported AI accelerators |
|--------------|---------------------------|
| CDNA 2 | MI210 |
| CDNA 3 | MI300X, MI325X |

### Supported NVIDIA AI Accelerators for INT4 (W4A16) Quantization

| Architecture | Supported AI accelerators | Minimum compute capability |
|--------------|---------------------------|----------------------------|
| Ampere | A10, A30, A40, A100 | 8.0 |
| Ada Lovelace | L4, L40, L40S | 8.9 |
| Hopper | H100, H200, GH200 | 9.0 |
| Blackwell | B200, B300, GB200, GB300 | 10.0 |

**Note:** NVIDIA Turing architecture (Tesla T4) does not have optimized vLLM kernel support for INT4 quantization. Use Ampere or newer architectures for INT4 inference.

### Supported AMD AI Accelerators for INT4 (W4A16) Quantization

| Architecture | Supported AI accelerators |
|--------------|---------------------------|
| CDNA 3 | MI300X, MI325X |

**Note:** AMD CDNA 2 architecture (MI210) does not have optimized vLLM kernel support for INT4 quantization.

### Supported NVIDIA AI Accelerators for FP8 (W8A8) Quantization

| Architecture | Supported AI accelerators | Minimum compute capability |
|--------------|---------------------------|----------------------------|
| Ada Lovelace | L4, L40, L40S | 8.9 |
| Hopper | H100, H200, GH200 | 9.0 |
| Blackwell | B200, B300, GB200, GB300 | 10.0 |

**Note:** NVIDIA Turing architecture (Tesla T4) and Ampere architecture (A10, A30, A40, A100) AI accelerators do not support FP8 W8A8 quantization due to hardware limitations. However, FP8 weight-only (W8A16) quantization is available on these architectures by using Marlin kernels.

### Supported AMD AI Accelerators for FP8 (W8A8) Quantization

| Architecture | Supported AI accelerators |
|--------------|---------------------------|
| CDNA 3 | MI300X, MI325X |

**Note:** AMD CDNA 2 architecture AI accelerators (MI210) do not support FP8 quantization due to hardware limitations.

### Supported NVIDIA AI Accelerators for NVFP4 Quantization

| Architecture | Supported AI accelerators | Minimum compute capability |
|--------------|---------------------------|----------------------------|
| Blackwell | B200, B300, GB200, GB300 | 10.0 |

**Note:** 
- NVFP4 quantization is only available on NVIDIA Blackwell architecture AI accelerators
- AMD AI accelerators do not support NVFP4 quantization

### Additional Resources

- [NVIDIA CUDA GPU compute capability](https://developer.nvidia.com/cuda-gpus)
- [vLLM quantization documentation](https://docs.vllm.ai/en/latest/quantization/)

---

## Chapter 5: Supported AI Accelerators for RHEL AI

The following AI accelerators are supported for inference serving with Red Hat AI Inference Server on RHEL AI.

**Important:** 
- Bare metal deployments of RHEL AI are supported for all NVIDIA CUDA and AMD ROCm AI accelerators listed in Supported AI accelerators for Red Hat AI Inference Server
- Actual requirements vary based on the specific models you deploy, quantization methods, context lengths, and concurrent request loads
- Aggregate GPU memory refers to the total GPU memory available across all GPUs in the system that can be used for tensor parallelism or pipeline parallelism
- **Recommended minimum additional disk storage for all platforms:** 1 TB

For more information about inference serving on bare metal or Cloud platforms, see Red Hat Enterprise Linux AI.

### Supported AI Accelerators for Amazon Web Services (AWS) Deployments

| NVIDIA AI accelerator | Aggregate GPU memory | AWS instance family |
|----------------------|---------------------|---------------------|
| GB200 | 384 GB | P6e series |
| B200 | 192 GB | P6 series |
| RTX PRO 6000 Blackwell Server Edition | 96 GB | G7e series |
| H100 | 80 GB | P5 series |
| L40S | 48 GB | G6e series |
| A100 | 40 GB | P4d series |
| L4 | 24 GB | G6 series |

### Supported AI Accelerators for IBM Cloud Deployments

| NVIDIA AI accelerator | Aggregate GPU memory | IBM Cloud instance family |
|----------------------|---------------------|---------------------------|
| H200 | 141 GB | gx3 series |
| H100 | 80 GB | gx3 series |
| A100 | 80 GB | gx3 series |
| L40S | 48 GB | gx3 series |
| L4 | 24 GB | gx3 series |

### Supported AI Accelerators for Microsoft Azure Deployments

| AI accelerator | Aggregate GPU memory | Azure instance family |
|----------------|---------------------|----------------------|
| NVIDIA GB200 | 384 GB | ND series |
| AMD Instinct MI300X | 192 GB | ND series |
| NVIDIA H100 | 80 GB | ND series |
| NVIDIA A100 | 80 GB | ND series |
| AMD Instinct MI210 | 64 GB | ND series |

### Supported AI Accelerators for Google Cloud Deployments

| NVIDIA AI accelerator | Aggregate GPU memory | Google Cloud instance family |
|----------------------|---------------------|------------------------------|
| GB200 | 384 GB | A4X series |
| B200 | 192 GB | A4 series |
| 4x L4 | 96 GB | G2 series |
| H100 | 80 GB | A3 series |
| A100 | 40 GB | A2 series |

---

## Chapter 6: Supported AI Accelerators for Red Hat OpenShift AI

You must install the AI accelerator Operator that is relevant to the AI accelerator that you want to use with Red Hat OpenShift AI.

OpenShift AI provides Operators that support integration with AI accelerators. OpenShift AI also provides images that include libraries that work with NVIDIA, AMD, and Intel Gaudi data center grade AI accelerators.

### Additional Resources

- [Enabling AI accelerators](https://access.redhat.com/documentation/en-us/red_hat_openshift_ai/)
- [Supported configurations](https://access.redhat.com/articles/rhoai-supported-configs)

---

## Chapter 7: Supported Deployment Environments

The following deployment environments for Red Hat AI Inference Server are supported.

**Note:** Red Hat AI Inference Server is available only as a container image. The host operating system and kernel must support the required accelerator drivers. For more information, see Supported AI accelerators.

### Red Hat AI Inference Server Supported Deployment Environments

| Environment | Supported versions | Deployment notes |
|-------------|-------------------|------------------|
| **OpenShift Container Platform (self-managed)** | 4.14+ | Deploy on bare-metal hosts or virtual machines |
| **Red Hat OpenShift Service on AWS (ROSA)** | 4.14+ | Requires a ROSA cluster with STS and GPU-enabled P5 or G5 node types. See Prepare your environment for more information |
| **Red Hat Enterprise Linux AI** | 3.0+ | Deploy on bare-metal hosts or virtual machines |
| **Red Hat Enterprise Linux (RHEL)** | 9.2+ | Deploy on bare-metal hosts or virtual machines |
| **Linux (not RHEL)** | - | Supported under third-party policy deployed on bare-metal hosts or virtual machines. OpenShift Container Platform Operators are not required |
| **Kubernetes (not OpenShift Container Platform)** | - | Supported under third-party policy deployed on bare-metal hosts or virtual machines |

**Important:**
- Single-host deployments for IBM Spyre AI accelerators on IBM Z and IBM Power are supported for RHEL AI 9.6+
- Cluster deployments for IBM Spyre AI accelerators on IBM Z are supported as part of Red Hat OpenShift AI version 3.0+ only

---

## Chapter 8: OpenShift Container Platform Software Prerequisites for GPU Deployments

The following table lists the minimum OpenShift Container Platform software prerequisites for AI accelerator workloads.

### Minimum Software Prerequisites for AI Accelerator Workloads

| Component | Minimum version | Operator |
|-----------|----------------|----------|
| NVIDIA GPU Operator | 24.3 | NVIDIA GPU Operator OLM Operator |
| AMD GPU Operator | 6.2 | AMD GPU Operator OLM Operator |
| IBM Spyre Operator | 1.0 | IBM Spyre Operator |
| Node Feature Discovery | 4.14 | Node Feature Discovery Operator[1] |

[1] Included by default with OpenShift Container Platform. Node Feature Discovery is required for scheduling NUMA-aware workloads.

---

## Chapter 9: Lifecycle and Update Policy

Security and critical bug fixes are delivered as container images available from the `registry.access.redhat.com/rhaiis` container registry and are announced through RHSA advisories.

See [RHAII container images on catalog.redhat.com](https://catalog.redhat.com/) for more details.
