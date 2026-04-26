---
title: "Deploying Models on Red Hat OpenShift AI Self-Managed 3.4"
source: "reference/Red_Hat_OpenShift_AI_Self-Managed-3.4-Deploying_models-en-US.pdf"
conversion_date: "2026-04-24"
conversion_method: "claude-optimized"
product: "Red Hat OpenShift AI"
version: "3.4"
document_type: "technical documentation"
topics:
  - model deployment
  - model storage
  - OCI containers
  - persistent volume claims
  - serving runtimes
  - KServe
  - vLLM
  - MLServer
  - NVIDIA NIM
  - inference endpoints
  - model monitoring
  - hardware profiles
  - deployment strategies
last_updated: "2026-04-22"
chunk_id: "deploying-models-full"
total_chunks: 4
---

# Deploying Models on Red Hat OpenShift AI Self-Managed 3.4

**Last Updated:** 2026-04-22

## Abstract

As a Red Hat OpenShift AI user, you can deploy your machine-learning models in Red Hat OpenShift AI Self-Managed.

---

## Chapter 1: Storing Models

You must store your model before you can deploy it. You can store a model in an S3 bucket, URI or Open Container Initiative (OCI) containers.

### 1.1 Using OCI Containers for Model Storage

As an alternative to storing a model in an S3 bucket or URI, you can upload models to Open Container Initiative (OCI) containers. Deploying models from OCI containers is also known as modelcars in KServe.

Using OCI containers for model storage can help you:

- Reduce startup times by avoiding downloading the same model multiple times
- Reduce disk space usage by reducing the number of models downloaded locally
- Improve model performance by allowing pre-fetched images

Using OCI containers for model storage involves the following tasks:

1. Storing a model in an OCI image
2. Deploying a model from an OCI image by using either the user interface or the command line interface

To deploy a model by using:
- **The user interface**: See Deploying models on the model serving platform
- **The command line interface**: See Deploying a model stored in an OCI image by using the CLI

### 1.2 Storing a Model in an OCI Image

You can store a model in an OCI image. The following procedure uses the example of storing a MobileNet v2-7 model in ONNX format.

#### Prerequisites

- You have a model in the ONNX format (this procedure uses the MobileNet v2-7 model)
- You have installed the Podman tool

#### Procedure

1. In a terminal window on your local machine, create a temporary directory for storing both the model and the support files:

```bash
# Create temporary working directory
# Context: Initial setup for OCI model storage
cd $(mktemp -d)
```

2. Create a models folder inside the temporary directory:

```bash
# Create numbered subdirectory for model versioning
# Context: OpenVINO requires numbered subdirectories for model versions
mkdir -p models/1
```

**Note:** This example specifies the subdirectory `1` because OpenVINO requires numbered subdirectories for model versioning. If you are not using OpenVINO, you do not need to create the `1` subdirectory.

3. Download the model and support files:

```bash
# Download ONNX model from official repository
# Context: Fetching MobileNet v2-7 model for containerization
DOWNLOAD_URL=https://github.com/onnx/models/raw/main/validated/vision/classification/mobilenet/model/mobilenetv2-7.onnx
curl -L $DOWNLOAD_URL -O --output-dir models/1/
```

4. Use the tree command to confirm the model files are in the expected directory structure:

```bash
# Verify directory structure
tree
```

Expected output:

```
.
├── Containerfile
└── models
    └── 1
        └── mobilenetv2-7.onnx
```

5. Create a Dockerfile named `Containerfile`:

**Important notes:**
- Specify a base image that provides a shell (e.g., ubi9-micro). You cannot use empty images like `scratch` because KServe uses the shell to ensure model files are accessible
- Change ownership and grant read permissions to the root group so the model server can access files (OpenShift runs containers with random user ID and root group ID)

```dockerfile
# OCI container image for model storage
# Context: Package model for KServe deployment
# Base: Red Hat UBI micro image (provides shell required by KServe)
FROM registry.access.redhat.com/ubi9/ubi-micro:latest

# Copy model files and set permissions for OpenShift random UIDs
COPY --chown=0:0 models /models
RUN chmod -R a=rX /models

# Run as nobody user for security
USER 65534
```

6. Use podman commands to create the OCI container image and upload to a registry:

**Note:** If your repository is private, authenticate to the registry before uploading.

```bash
# Build and push OCI model container
# Context: Publishing model image to Quay registry
# Prerequisites: Authenticated to registry if private
podman build --format=oci -t quay.io/<user_name>/<repository_name>:<tag_name> .
podman push quay.io/<user_name>/<repository_name>:<tag_name>
```

### 1.3 Uploading Model Files to a Persistent Volume Claim (PVC)

When deploying a model, you can serve it from a preexisting Persistent Volume Claim (PVC) where your model files are stored. You can upload your local model files to a PVC in the IDE that you access from a running workbench.

#### Prerequisites

- You have access to the OpenShift AI dashboard
- You have access to a project that has a running workbench
- You have created a persistent volume claim (PVC) with context type of Model storage
- The workbench is attached to the persistent volume (PVC)
- You have the model files saved on your local machine

#### Procedure

Follow these steps to upload your model files to the PVC mount point (`/opt/app-root/src/`) within your workbench:

1. From the OpenShift AI dashboard, click the open icon to open your IDE in a new window

2. In your IDE, navigate to the File Browser pane:
   - In **JupyterLab**: Usually labeled "Files"
   - In **code-server**: Usually the Explorer view

3. In the file browser, navigate to the `/opt/app-root/src/` folder (this represents the root of your attached PVC)

**Note:** Any files or folders you create or upload to this folder persist in the PVC.

4. **Optional:** Create a new folder to organize your models:
   a. Right-click within `/opt/app-root/src/` and select **New Folder**
   b. Name the folder (e.g., `models`)
   c. Double-click the new folder to enter it

5. Upload your model files to the current folder:

**Using JupyterLab:**
   a. Click the Upload Files icon in the file browser toolbar
   b. Navigate to and select model files from your local computer, click **Open**
   c. Wait for upload progress bars to complete

**Using code-server:**
   a. Drag model files directly from your local file explorer
   b. Drop them into the file browser pane in the target folder

6. Wait for the upload process to complete

#### Verification

Confirm that your files appear in the file browser at the path where you uploaded them.

#### Next Steps

When you deploy a model, access the model files from the PVC:

1. In the **Deploy model** dialog, select **Existing cluster storage** under Source model location
2. From the **Cluster storage** list, select the PVC associated with your workbench
3. In the **Model path** field, enter the path to your model or folder containing your model

---

## Chapter 2: Deploying Models

The model serving platform is based on the KServe component and deploys each model from its own dedicated model server. This architecture is ideal for deploying, monitoring, scaling, and maintaining large models that require more resources, such as large language models (LLMs).

### 2.1 Automatic Selection of Serving Runtimes

When you deploy a model, OpenShift AI can automatically select the best serving runtime for your deployment. This feature allows you to efficiently deploy applications without needing to manually research runtime compatibility. The system determines the optimal runtime by analyzing the model type, model format, and selected hardware profile.

#### 2.1.1 Hardware Profile Matching

The system suggests a runtime by matching the accelerator defined in your selected hardware profile with available runtimes. For example, if you select a hardware profile that uses an NVIDIA GPU accelerator, the system filters for compatible runtimes, such as vLLM NVIDIA GPU ServingRuntime for KServe.

**Note:** Automatic selection is available only if a hardware profile exists for the specific accelerator you want to use.

#### 2.1.2 Predictive Model Selection

For predictive models, you must select a **Model format** before the system can determine the appropriate serving runtime.

#### 2.1.3 Selection Limitations

The Auto-select option is displayed only when the system can identify a single, distinct match. If multiple serving runtime templates are defined for the same accelerator, the system cannot determine the best option automatically. In such cases, you must manually select a runtime.

#### 2.1.4 Manual Serving Runtime Selection

You can manually select a specific runtime from the **Serving runtime** list if the automatically selected option does not meet your needs. This is useful when you require a specific version or want to use a custom runtime. The list displays all global and project-scoped serving runtime templates available to you.

#### 2.1.5 Administrator Overrides

Cluster administrator settings can override standard hardware profile matching. If the **Use distributed inference with llm-d by default when deploying generative models** option is enabled in administrator settings, the system defaults to the Distributed inference with llm-d runtime, regardless of other potential matches.

This option is available in: **Settings → Cluster settings → General settings**

### 2.2 Deployment Strategies for Resource Optimization

To optimize resource usage and manage downtime during model rollouts, you can configure the deployment strategy for your inference services. Choosing the appropriate strategy depends on your cluster's available quotas, especially hardware accelerators such as GPUs, and your tolerance for service interruptions.

There are two primary deployment strategies:

#### Rolling Update

This strategy ensures **zero downtime** and continuous availability of the model. New inference service pods start while existing pods are running. Traffic switches to new pods only after they are fully ready, then old pods are terminated.

**Resource impact:** Rolling updates require increased resources like CPU, memory, and GPUs during the update process. Plan for approximately **200% of the pod requests** as headroom during the transition because parallel instances exist briefly.

#### Recreate

This strategy prioritizes **resource conservation** over availability. All existing inference service pods are terminated before new pods attempt to launch.

**Resource impact:** This method requires a period of downtime. The model endpoint is unavailable and returns errors between termination of the old pod and readiness of the new pod.

#### 2.2.1 Choosing a Deployment Strategy

Choose the deployment strategy that best fits your availability requirements and resource quotas.

| Strategy | Description | Resource Impact | Recommended Scenarios |
|----------|-------------|-----------------|----------------------|
| **Rolling update** | Replaces pods gradually to ensure zero downtime. Traffic switches to new pods only after they are fully ready. | **High:** Requires approximately 200% of request resources to host parallel instances during transition. | • **Production workloads:** Environments where the model must remain accessible without interruption<br>• **High-quota clusters:** Namespaces with sufficient headroom to accommodate parallel instances |
| **Recreate** | Terminates the old pod before starting the new one. Service is unavailable during the transition. | **Low:** Consumption does not exceed 100%. Prevents Insufficient Resources errors. | • **Resource-constrained environments:** Projects using scarce hardware like high-end GPUs<br>• **Development and staging:** Environments where downtime doesn't impact business operations<br>• **Batch processing:** Workflows where immediate availability is not critical<br>• **Maintenance windows:** Periods where service unavailability is expected |

**Important:** The Recreate strategy severs the connection to the old pod immediately. Ensure that your traffic routing gateway and client applications can handle a temporary gap in service before applying this strategy.

**Note:** The Recreate deployment strategy is available for all runtimes except **Distributed inference with llm-d**. If you select that runtime, deployment strategy options are not displayed and the system defaults to Recreate.

### 2.3 Deploying Models on the Model Serving Platform

You can deploy generative AI (gen AI) or predictive AI models on the model serving platform using the **Deploy a model** wizard. The wizard allows you to configure your model, including specifying its location and type, selecting a serving runtime, assigning a hardware profile, and setting advanced configurations like external routes and token authentication.

#### General Prerequisites

- You have logged in to Red Hat OpenShift AI
- You have installed KServe and enabled the model serving platform
- You have enabled a preinstalled or custom model-serving runtime
- You have created a project
- You have access to S3-compatible object storage, a URI-based repository, an OCI-compliant registry, or a persistent volume claim (PVC) and have added a connection to your project
- If using GPUs: You have enabled GPU support in OpenShift AI (for NVIDIA GPUs, see Enabling NVIDIA GPUs; for AMD GPUs, see AMD GPU integration)

#### Runtime-Specific Prerequisites

**Caikit-TGIS runtime:**
- You have converted your model to Caikit format (see Converting Hugging Face Hub models to Caikit format in the caikit-tgis-serving repository)

**vLLM NVIDIA GPU ServingRuntime for KServe:**
- You have enabled GPU support and installed the Node Feature Discovery Operator on your cluster

**vLLM CPU ServingRuntime for KServe:**
- For IBM Z and IBM Power, use the vLLM CPU ServingRuntime (GPU accelerators are not available on these architectures)

**vLLM Intel Gaudi Accelerator ServingRuntime for KServe:**
- You have enabled support for hybrid processing units (HPUs) including installing the Intel Gaudi Base Operator and configuring a hardware profile

**vLLM AMD GPU ServingRuntime for KServe:**
- You have enabled AMD GPU support including installing the AMD GPU operator and configuring a hardware profile

**vLLM Spyre AI Accelerator ServingRuntime for KServe:**

**Important:** Support for IBM Spyre AI Accelerators on x86 is currently a **Technology Preview** feature in Red Hat OpenShift AI 3.4 (not supported with production SLAs). Support on ppc64le is **General Availability (GA)**.

- For x86 and ppc64le: You have installed the Spyre Operator and configured a hardware profile

**vLLM Spyre s390x ServingRuntime for KServe:**
- You have installed the Spyre Operator and configured a hardware profile for IBM Z

#### Procedure

1. In the left menu, click **Projects**

2. Click the name of the project where you want to deploy a model

3. Click the **Deployments** tab

4. Click **Deploy model** (the Deploy a model wizard opens)

5. In the **Model details** section, provide information about the model:

   a. From the **Model location** list, specify where your model is stored and complete the connection detail fields

   **Note:** 
   - The OCI-compliant registry, S3 compatible object storage, and URI options are preinstalled connection types
   - If you uploaded model files to a PVC attached to your workbench, the **Cluster storage** option becomes available

   b. From the **Model type** list, select **Generative AI model** or **Predictive**

   c. Click **Next**

6. In the **Model deployment** section, configure the deployment:

   a. In the **Model deployment name** field, enter a unique name

   b. In the **Description** field, enter a description

   c. From the **Hardware profile** list, select a hardware profile

   d. **Optional:** To modify default resource allocation, click **Customize resource requests and limits** and enter new values for CPU and Memory requests/limits

   e. In the **Serving runtime** field, select one of the following:
      - **Auto-select the best runtime** based on model type, format, and hardware profile
      - **Select from a list of serving runtimes** to manually choose (includes custom runtimes)

   f. **Optional:** If you selected Predictive model type, select a framework from the **Model framework** list (hidden for Generative AI models)

   g. In the **Number of model server replicas** field, specify a value

   h. Click **Next**

7. In the **Advanced settings** section, configure advanced options:

   a. **Optional (Generative AI only):** Select **Add as AI asset endpoint** to add your model's endpoint to Gen AI studio → AI asset endpoints
      - Enter the **Use case** (e.g., chat, multimodal, natural language processing)
      
   **Note:** You must add your model as an AI asset endpoint to test it on the Gen AI studio playground page.

   b. **Optional:** Select **Model access** checkbox to make your deployment available through an external route

   c. **Optional:** Select **Require token authentication** for inference requests

   d. In the **Service account name** field, enter the service account name for token generation

   e. Click **Add a service account** to add additional service accounts

   f. **Optional:** Select **Add custom runtime arguments** or **Add custom runtime environment variables** to add configuration parameters

   g. In the **Deployment strategy** section, select **Rolling update** or **Recreate**

   **Note:** Recreate is available for all runtimes except Distributed inference with llm-d (which always uses Recreate).

8. Click **Deploy**

#### Verification

Confirm that the deployed model is shown on the Deployments tab for the project, and on the Deployments page with a checkmark in the Status column.

#### Additional Resources

- Model-serving runtimes for accelerators
- Adding a custom model-serving runtime
- Deployment strategies for resource optimization

### 2.4 Deploying Models by Using the MLServer Runtime

Deploy models with the MLServer ServingRuntime for KServe by specifying the model implementation and URI using environment variables in the Deploy a model wizard.

**Important:** MLServer ServingRuntime for KServe is currently a **Technology Preview** feature (not supported with production SLAs).

#### Prerequisites

- You have logged in to Red Hat OpenShift AI
- You have installed KServe and enabled the model serving platform
- The MLServer ServingRuntime for KServe is enabled in your cluster
- You have created a project
- Your model is stored in a location accessible to the model server with a connection added to your project:
  - S3-compatible object storage
  - Persistent Volume Claim
- You are deploying a model that uses a supported MLServer implementation:
  - Scikit-learn
  - XGBoost
  - LightGBM

**Note:** The model name is automatically exported from the model deployment name. You do not need to set `MLSERVER_MODEL_NAME` environment variable manually. If you do, it must match your model deployment name.

**Important:** You can also use MLServer's `model-settings.json` file for model configuration. If a `model-settings.json` file is present alongside your model file, the MLServer runtime loads configuration values from that file and overrides any environment variables you set through the deployment wizard.

#### Procedure

1. Deploy the model using the **Deploy a model** wizard (see Deploying models on the model serving platform for complete instructions)

2. In the **Advanced settings** section, configure environment variables:

   a. Under **Configuration parameters**, select **Add custom runtime environment variables**

   b. Click **Add variable**

   c. Add appropriate variables for your model framework:

**For XGBoost models:**

| Key | Value |
|-----|-------|
| `MLSERVER_MODEL_IMPLEMENTATION` | `mlserver_xgboost.XGBoostModel` |
| `MLSERVER_MODEL_URI` | `/mnt/models/model.json` |

**For Scikit-learn models:**

| Key | Value |
|-----|-------|
| `MLSERVER_MODEL_IMPLEMENTATION` | `mlserver_sklearn.SKLearnModel` |
| `MLSERVER_MODEL_URI` | `/mnt/models/model.joblib` |

**For LightGBM models:**

| Key | Value |
|-----|-------|
| `MLSERVER_MODEL_IMPLEMENTATION` | `mlserver_lightgbm.LightGBMModel` |
| `MLSERVER_MODEL_URI` | `/mnt/models/model.bst` |

**Note:** For `MLSERVER_MODEL_URI`, you can specify either:
- **Absolute path:** An absolute path to a specific model file (e.g., `/mnt/models/model.json`)
- **Directory path:** A directory path (e.g., `/mnt/models`). If using a directory path, your model file must use well-known filenames:
  - XGBoost: `model.bst`, `model.json`, `model.ubj`
  - LightGBM: `model.bst`
  - Scikit-learn: `model.joblib`, `model.pickle`, `model.pkl`

#### Verification

- Confirm the deployed model appears on the Deployments tab with a checkmark in the Status column
- Test the model by querying the ready endpoint:

```bash
# Test MLServer model readiness
# Context: Verify model is ready to serve inference requests
# Prerequisites: Model deployed successfully
curl -H "Content-Type: application/json" \
  https://<inference_endpoint_url>/v2/models/<model_name>/ready
```

Replace:
- `<inference_endpoint_url>`: The inference endpoint URL displayed in model details
- `<model_name>`: The name of your deployed model

#### Additional Resources

- Inference endpoints
- Deploying models on the model serving platform
- MLServer XGBoost Runtime
- MLServer scikit-learn Runtime
- MLServer LightGBM Runtime
- MLServer OpenAPI / Inference API

### 2.5 Deploying a Model Stored in an OCI Image by Using the CLI

You can deploy a model stored in an OCI image from the command line interface. The following procedure uses the example of deploying a MobileNet v2-7 model in ONNX format, stored in an OCI image on an OpenVINO model server.

**Note:** By default in KServe, models are exposed outside the cluster and not protected with authentication.

#### Prerequisites

- You have stored a model in an OCI image (see Storing a model in an OCI image)
- If deploying from a private OCI repository, you must configure an image pull secret (see Using image pull secrets)
- You are logged in to your OpenShift cluster

#### Procedure

1. Create a project to deploy the model:

```bash
# Create dedicated project for OCI model deployment
# Context: Isolated namespace for model serving resources
oc new-project oci-model-example
```

2. Use the OpenShift AI Applications project kserve-ovms template to create a ServingRuntime resource:

```bash
# Deploy OpenVINO model server runtime
# Context: Configure ServingRuntime for ONNX model inference
# Prerequisites: OpenShift AI operator installed
oc process -n redhat-ods-applications -o yaml kserve-ovms | oc apply -f -
```

3. Verify the ServingRuntime named `kserve-ovms` is created:

```bash
# Check serving runtimes available in namespace
oc get servingruntimes
```

Expected output:

```
NAME          DISABLED   MODELTYPE    CONTAINERS         AGE
kserve-ovms              openvino_ir  kserve-container   1m
```

4. Create an InferenceService YAML resource:

**For a model in a public OCI repository:**

```yaml
# InferenceService using public OCI model image
# Context: Deploy MobileNet model from public Quay registry
# Runtime: OpenVINO Model Server for ONNX inference
apiVersion: serving.kserve.io/v1beta1
kind: InferenceService
metadata:
  name: sample-isvc-using-oci
spec:
  predictor:
    model:
      runtime: kserve-ovms  # Must match ServingRuntime name
      modelFormat:
        name: onnx
      storageUri: oci://quay.io/<user_name>/<repository_name>:<tag_name>
      resources:
        requests:
          memory: 500Mi
          cpu: 100m
          # nvidia.com/gpu: "1"  # Uncomment if using GPU
        limits:
          memory: 4Gi
          cpu: 500m
          # nvidia.com/gpu: "1"  # Uncomment if using GPU
```

**For a model in a private OCI repository:**

```yaml
# InferenceService using private OCI model image
# Context: Deploy model from private registry with pull secret
# Prerequisites: Image pull secret created in namespace
apiVersion: serving.kserve.io/v1beta1
kind: InferenceService
metadata:
  name: sample-isvc-using-private-oci
spec:
  predictor:
    model:
      runtime: kserve-ovms  # Must match ServingRuntime name
      modelFormat:
        name: onnx
      storageUri: oci://quay.io/<user_name>/<repository_name>:<tag_name>
      resources:
        requests:
          memory: 500Mi
          cpu: 100m
          # nvidia.com/gpu: "1"  # Uncomment if using GPU
        limits:
          memory: 4Gi
          cpu: 500m
          # nvidia.com/gpu: "1"  # Uncomment if using GPU
    imagePullSecrets:
      - name: <pull-secret-name>
```

After creating the InferenceService resource, KServe deploys the model stored in the OCI image referred to by the `storageUri` field.

#### Verification

Check the status of the deployment:

```bash
# Check InferenceService status and readiness
# Context: Verify model deployment completed successfully
oc get inferenceservice
```

The command returns output including the URL of the deployed model and its readiness state.

### 2.6 Monitoring Models

You can monitor models deployed on the model serving platform to view performance and resource usage metrics.

#### 2.6.1 Viewing Performance Metrics for a Deployed Model

You can monitor the following metrics for a specific model deployed on the model serving platform:

- **Number of requests:** The number of requests that have failed or succeeded
- **Average response time (ms):** The average time it takes the model to respond to requests
- **CPU utilization (%):** The percentage of the CPU limit per model replica currently utilized
- **Memory utilization (%):** The percentage of the memory limit per model replica utilized

You can specify a time range and refresh interval for these metrics to help determine peak usage hours and model performance at specified times.

##### Prerequisites

- You have installed Red Hat OpenShift AI
- A cluster admin has enabled user workload monitoring (UWM) for user-defined projects on your OpenShift cluster (see Enabling monitoring for user-defined projects and Configuring monitoring for the model serving platform)
- You have logged in to Red Hat OpenShift AI
- The following dashboard configuration options are set to default values:
  - `disablePerformanceMetrics: false`
  - `disableKServeMetrics: false`
  (For more information, see Customizing the dashboard)
- You have deployed a model on the model serving platform using a preinstalled runtime

**Note:** Metrics are only supported for models deployed using a preinstalled model-serving runtime or a custom runtime duplicated from a preinstalled runtime.

##### Procedure

1. From the OpenShift AI dashboard navigation menu, click **Projects**

2. Click the name of the project containing the data science models you want to monitor

3. In the project details page, click the **Deployments** tab

4. Select the model you are interested in

5. On the **Endpoint performance** tab, set the following options:

   - **Time range:** Specifies how long to track the metrics
     - Options: 1 hour, 24 hours, 7 days, 30 days
   
   - **Refresh interval:** Specifies how frequently the graphs are refreshed
     - Options: 15 seconds, 30 seconds, 1 minute, 5 minutes, 15 minutes, 30 minutes, 1 hour, 2 hours, 1 day

6. Scroll down to view data graphs for number of requests, average response time, CPU utilization, and memory utilization

##### Verification

The Endpoint performance tab shows graphs of metrics for the model.

#### 2.6.2 Viewing Model-Serving Runtime Metrics for the Model Serving Platform

When a cluster administrator has configured monitoring for the model serving platform, non-admin users can use the OpenShift web console to view model-serving runtime metrics for the KServe component.

##### Prerequisites

- A cluster administrator has configured monitoring for the model serving platform
- You have been assigned the `monitoring-rules-view` role (see Granting users permission to configure monitoring for user-defined projects)
- You are familiar with how to monitor project metrics in the OpenShift web console (see Monitoring your project metrics)

##### Procedure

1. Log in to the OpenShift web console

2. Switch to the **Developer** perspective

3. In the left menu, click **Observe**

4. Use the web console to run queries for model-serving runtime metrics. You can also run queries for metrics related to OpenShift Service Mesh. Examples:

**For vLLM runtime:**

```promql
# Number of successful inference requests for vLLM model
# Context: Track successful request count over time
# Note: Certain vLLM metrics are only available after processing an inference request
sum(increase(vllm:request_success_total{namespace=${namespace},model_name=${model_name}}[${rate_interval}]))
```

**Important:** Certain vLLM metrics are available only after an inference request is processed. To generate and view these metrics, you must first make an inference request to the model.

**For OpenVINO Model Server runtime:**

```promql
# Number of successful inference requests for OpenVINO model
# Context: Track successful request count over time
sum(increase(ovms_requests_success{namespace=${namespace},name=${model_name}}[${rate_interval}]))
```

##### Additional Resources

- OVMS metrics

---

## Chapter 3: Deploying Models on the NVIDIA NIM Model Serving Platform

You can deploy models using NVIDIA NIM inference services on the NVIDIA NIM model serving platform. NVIDIA NIM, part of NVIDIA AI Enterprise, is a set of microservices designed for secure, reliable deployment of high-performance AI model inferencing across clouds, data centers, and workstations.

### 3.1 Deploying Models on the NVIDIA NIM Model Serving Platform

When you have enabled the NVIDIA NIM model serving platform, you can start deploying NVIDIA-optimized models on the platform.

#### Prerequisites

- You have logged in to Red Hat OpenShift AI
- You have enabled the NVIDIA NIM model serving platform
- You have created a project
- You have enabled support for graphics processing units (GPUs) in OpenShift AI, including:
  - Installing the Node Feature Discovery Operator
  - Installing the NVIDIA GPU Operator
  (See Installing the Node Feature Discovery Operator and Enabling NVIDIA GPUs)

#### Procedure

1. In the left menu, click **Projects**

2. Click the name of the project where you want to deploy a model

3. Click the **Deployments** tab

4. In the **Deployments** section, perform one of the following:
   - On the **NVIDIA NIM model serving platform** tile, click **Select NVIDIA NIM**, then click **Deploy model**
   - If you have previously selected NVIDIA NIM model serving type, the Deployments page displays "NVIDIA model serving enabled" on the upper-right corner with the **Deploy model** button

5. Configure properties for deploying your model:

   a. In the **Model deployment name** field, enter a unique name for the deployment

   b. From the **NVIDIA NIM** list, select the NVIDIA NIM model you want to deploy (see Supported Models)

   c. In the **NVIDIA NIM storage size** field, specify the size of the cluster storage instance that will store the NVIDIA NIM model

   **Note:** When resizing a PersistentVolumeClaim (PVC) backed by Amazon EBS in OpenShift AI, you may encounter `VolumeModificationRateExceeded: You've reached the maximum modification rate per volume limit`. To avoid this error, wait at least six hours between modifications per EBS volume. This is an Amazon EBS service limit that applies to all workloads using EBS-backed PVCs.

   d. In the **Number of model server replicas** field, specify a value

   e. From the **Model server size** list, select a value

6. From the **Hardware profile** list, select a hardware profile

7. **Optional:** Click **Customize resource requests and limits** and update:

   a. **CPU requests:** Number of CPUs to use (specify in cores or millicores)

   b. **CPU limits:** Maximum number of CPUs to use (specify in cores or millicores)

   c. **Memory requests:** Requested memory in gibibytes (Gi)

   d. **Memory limits:** Maximum memory limit in gibibytes (Gi)

8. **Optional:** In the **Model route** section, select **Make deployed models available through an external route** to make models available to external clients

9. To require token authentication for inference requests:

   a. Select **Require token authentication**

   b. In the **Service account name** field, enter the service account name for token generation

   c. Click **Add a service account** to add additional service accounts

10. Click **Deploy**

#### Verification

Confirm that the deployed model is shown on the Deployments tab for the project, and on the Deployments page with a checkmark in the Status column.

#### Additional Resources

- NVIDIA NIM API reference
- Supported Models

### 3.2 Viewing NVIDIA NIM Metrics for a NIM Model

In OpenShift AI, you can observe the following NVIDIA NIM metrics for a NIM model deployed on the NVIDIA NIM model serving platform:

- GPU cache usage over time (ms)
- Current running, waiting, and max requests count
- Tokens count
- Time to first token
- Time per output token
- Request outcomes

You can specify a time range and refresh interval for these metrics to help determine peak usage hours and model performance at specified times.

#### Prerequisites

- You have enabled the NVIDIA NIM model serving platform
- You have deployed a NIM model on the NVIDIA NIM model serving platform
- A cluster administrator has enabled metrics collection and graph generation for your deployment
- The `disableKServeMetrics` OpenShift AI dashboard configuration option is set to default value (`false`)

#### Procedure

1. From the OpenShift AI dashboard navigation menu, click **Projects**

2. Click the name of the project containing the NIM model you want to monitor

3. In the project details page, click the **Deployments** tab

4. Click the NIM model you want to observe

5. On the **NIM Metrics** tab, set the following options:

   - **Time range:** How long to track metrics
     - Options: 1 hour, 24 hours, 7 days, 30 days
   
   - **Refresh interval:** How frequently graphs are refreshed
     - Options: 15 seconds, 30 seconds, 1 minute, 5 minutes, 15 minutes, 30 minutes, 1 hour, 2 hours, 1 day

6. Scroll down to view data graphs for NIM metrics

#### Verification

The NIM Metrics tab shows graphs of NIM metrics for the deployed NIM model.

#### Additional Resources

- NVIDIA NIM observability

### 3.3 Viewing Performance Metrics for a NIM Model

You can observe the following performance metrics for a NIM model deployed on the NVIDIA NIM model serving platform:

- **Number of requests:** The number of requests that have failed or succeeded
- **Average response time (ms):** The average time it takes the model to respond to requests
- **CPU utilization (%):** The percentage of the CPU limit per model replica currently utilized
- **Memory utilization (%):** The percentage of the memory limit per model replica utilized

You can specify a time range and refresh interval for these metrics.

#### Prerequisites

- You have enabled the NVIDIA NIM model serving platform
- You have deployed a NIM model on the NVIDIA NIM model serving platform
- A cluster administrator has enabled metrics collection and graph generation
- The `disableKServeMetrics` configuration option is set to default value (`false`)

#### Procedure

1. From the OpenShift AI dashboard navigation menu, click **Projects**

2. Click the name of the project containing the NIM model you want to monitor

3. In the project details page, click the **Deployments** tab

4. Click the NIM model you want to observe

5. On the **Endpoint performance** tab, set the following options:

   - **Time range:** How long to track metrics
     - Options: 1 hour, 24 hours, 7 days, 30 days
   
   - **Refresh interval:** How frequently graphs are refreshed
     - Options: 15 seconds, 30 seconds, 1 minute, 5 minutes, 15 minutes, 30 minutes, 1 hour, 2 hours, 1 day

6. Scroll down to view data graphs for performance metrics

#### Verification

The Endpoint performance tab shows graphs of performance metrics for the deployed NIM model.

---

## Chapter 4: Making Inference Requests to Deployed Models

When you deploy a model, it is available as a service that you can access with API requests. This allows you to get predictions from your model based on the data you provide in the request.

### 4.1 Accessing the Authentication Token for a Deployed Model

If you secured your model inference endpoint by enabling token authentication, you must know how to access your authentication token so that you can specify it in your inference requests.

#### Prerequisites

- You have logged in to Red Hat OpenShift AI
- You have deployed a model using the model serving platform

#### Procedure

1. From the OpenShift AI dashboard, click **Projects**

2. Click the name of the project containing your deployed model

3. Click the **Deployments** tab

4. In the Deployments list, expand the section for your model

   Your authentication token is shown in the **Token authentication** section, in the **Token secret** field

5. **Optional:** To copy the authentication token for use in an inference request, click the Copy button next to the token value

### 4.2 Accessing the Inference Endpoint for a Deployed Model

To make inference requests to your deployed model, you must know how to access the inference endpoint that is available.

For a list of paths to use with the supported runtimes and example commands, see Inference endpoints.

#### Prerequisites

- You have logged in to Red Hat OpenShift AI
- You have deployed a model using the model serving platform
- If you enabled token authentication, you have the associated token value

#### Procedure

1. From the OpenShift AI dashboard, click **AI hub → Deployments**

   The inference endpoint for the model is shown in the **Inference endpoints** field

2. Depending on what action you want to perform with the model (and if the model supports that action), copy the inference endpoint and add a path to the end of the URL

3. Use the endpoint to make API requests to your deployed model

#### Additional Resources

- Caikit API documentation
- OpenVINO KServe-compatible REST API documentation
- OpenAI API documentation

### 4.3 Making Inference Requests to Models Deployed on the Model Serving Platform

When you deploy a model using the model serving platform, the model is available as a service that you can access using API requests. This enables you to return predictions based on data inputs. To use API requests to interact with your deployed model, you must know the inference endpoint for the model. In addition, if you secured your inference endpoint by enabling token authentication, you must know how to access your authentication token.

### 4.4 Inference Endpoints

These examples show how to use inference endpoints to query the model.

**Note:** If you enabled token authentication when deploying the model, add the `Authorization` header and specify a token value.

#### 4.4.1 Caikit TGIS ServingRuntime for KServe

**Endpoints:**

- `:443/api/v1/task/text-generation`
- `:443/api/v1/task/server-streaming-text-generation`

**Example command:**

```bash
# Caikit TGIS text generation inference request
# Context: Stream text generation from Caikit-served model
# Prerequisites: Model deployed with Caikit-TGIS runtime, token available if auth enabled
curl --json '{"model_id": "<model_name>", "inputs": "<text>"}' \
  https://<inference_endpoint_url>:443/api/v1/task/server-streaming-text-generation \
  -H 'Authorization: Bearer <token>'
```

#### 4.4.2 OpenVINO Model Server

**Endpoint:**

- `/v2/models/<model-name>/infer`

**Example command:**

```bash
# OpenVINO Model Server inference request
# Context: Send inference data to ONNX/OpenVINO model
# Prerequisites: Model deployed with OpenVINO runtime
curl -ks <inference_endpoint_url>/v2/models/<model_name>/infer \
  -d '{
    "model_name": "<model_name>",
    "inputs": [{
      "name": "<name_of_model_input>",
      "shape": [<shape>],
      "datatype": "<data_type>",
      "data": [<data>]
    }]
  }' \
  -H 'Authorization: Bearer <token>'
```

#### 4.4.3 vLLM NVIDIA GPU ServingRuntime for KServe

**Endpoints:**

- `:443/version`
- `:443/docs`
- `:443/v1/models`
- `:443/v1/chat/completions`
- `:443/v1/completions`
- `:443/v1/embeddings`
- `:443/tokenize`
- `:443/detokenize`

**Important notes:**

- The vLLM runtime is **compatible with the OpenAI REST API**
- To use the embeddings inference endpoint, you must use an embeddings model that vLLM supports (you cannot use embeddings endpoint with generative models; see Supported embeddings models in vLLM)
- As of vLLM v0.5.5, you must provide a **chat template** while querying a model using the `/v1/chat/completions` endpoint. If your model doesn't include a predefined chat template, specify one using the `chat-template` command-line parameter in your custom vLLM runtime:

```yaml
# Custom vLLM runtime with chat template
# Context: Configure chat template for models without predefined template
containers:
  - args:
      - --chat-template=<CHAT_TEMPLATE>
```

Chat templates are available as `.jinja` files or with the vLLM image under `/opt/app-root/template`. For more information, see Chat templates.

As indicated by the paths shown, the model serving platform uses the HTTPS port of your OpenShift router (usually port 443) to serve external API requests.

**Example command:**

```bash
# vLLM chat completion inference request
# Context: OpenAI-compatible chat completion endpoint
# Prerequisites: vLLM model deployed, chat template configured
curl -v https://<inference_endpoint_url>:443/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [{
      "role": "<role>",
      "content": "<content>"
    }]
  }' \
  -H 'Authorization: Bearer <token>'
```

#### 4.4.4 vLLM Intel Gaudi Accelerator ServingRuntime for KServe

See vLLM NVIDIA GPU ServingRuntime for KServe.

#### 4.4.5 vLLM AMD GPU ServingRuntime for KServe

See vLLM NVIDIA GPU ServingRuntime for KServe.

#### 4.4.6 vLLM Spyre AI Accelerator ServingRuntime for KServe

**Important:** Support for IBM Spyre AI Accelerators on x86 is currently a **Technology Preview** feature (not supported with production SLAs).

You can serve models with IBM Spyre AI accelerators on x86 by using the vLLM Spyre AI Accelerator ServingRuntime for KServe runtime. To use the runtime, you must install the Spyre Operator and configure a hardware profile. For more information, see Spyre operator image and Working with hardware profiles.

#### 4.4.7 vLLM Spyre s390x ServingRuntime for KServe

You can serve models with IBM Spyre AI accelerators on IBM Z (s390x architecture) by using the vLLM Spyre s390x ServingRuntime for KServe runtime. To use the runtime, you must install the Spyre Operator and configure a hardware profile. For more information, see Spyre operator image and Working with hardware profiles.

#### 4.4.8 vLLM Spyre ppc64le ServingRuntime for KServe

You can serve models with IBM Spyre AI accelerators on IBM Power (ppc64le architecture) by using the vLLM Spyre ppc64le ServingRuntime for KServe runtime. To use the runtime, you must install the Spyre Operator and configure a hardware profile. For more information, see Spyre operator image and Working with hardware profiles.

#### 4.4.9 NVIDIA Triton Inference Server

**REST endpoints:**

- `v2/models/[/versions/<model_version>]/infer`
- `v2/models/<model_name>[/versions/<model_version>]`
- `v2/health/ready`
- `v2/health/live`
- `v2/models/<model_name>[/versions/]/ready`
- `v2`

**Example REST command:**

```bash
# NVIDIA Triton inference request (REST)
# Context: Send inference request to Triton-served model
# Prerequisites: Model deployed with Triton runtime
curl -ks <inference_endpoint_url>/v2/models/<model_name>/infer \
  -d '{
    "model_name": "<model_name>",
    "inputs": [{
      "name": "<name_of_model_input>",
      "shape": [<shape>],
      "datatype": "<data_type>",
      "data": [<data>]
    }]
  }' \
  -H 'Authorization: Bearer <token>'
```

**gRPC endpoints:**

- `:443 inference.GRPCInferenceService/ModelInfer`
- `:443 inference.GRPCInferenceService/ModelReady`
- `:443 inference.GRPCInferenceService/ModelMetadata`
- `:443 inference.GRPCInferenceService/ServerReady`
- `:443 inference.GRPCInferenceService/ServerLive`
- `:443 inference.GRPCInferenceService/ServerMetadata`

**Example gRPC command:**

```bash
# NVIDIA Triton inference request (gRPC)
# Context: Query model metadata via gRPC endpoint
# Prerequisites: Triton model deployed, gRPC client installed, CA cert available
grpcurl -cacert ./openshift_ca_istio_knative.crt \
  -proto ./grpc_predict_v2.proto \
  -d @ \
  -H "Authorization: Bearer <token>" \
  <inference_endpoint_url>:443 inference.GRPCInferenceService/ModelMetadata
```

#### 4.4.10 MLServer ServingRuntime for KServe

**Important:** MLServer ServingRuntime for KServe is currently a **Technology Preview** feature (not supported with production SLAs).

**Note:** HTTP requests to the MLServer ServingRuntime for KServe require the `Content-Type: application/json` header.

**REST endpoints:**

- `/v2`
- `/v2/health/live`
- `/v2/health/ready`
- `/v2/models/{model_name}`
- `/v2/models/{model_name}/infer`
- `/v2/models/{model_name}/ready`

**Example command:**

```bash
# MLServer inference request
# Context: Send inference data to MLServer-served model
# Prerequisites: MLServer model deployed with proper environment variables
curl -ks <inference_endpoint_url>/v2/models/<model_name>/infer \
  -H "Content-Type: application/json" \
  -d '{
    "inputs": [{
      "name": "<name_of_model_input>",
      "shape": [<shape>],
      "datatype": "<data_type>",
      "data": [<data>]
    }]
  }' \
  -H 'Authorization: Bearer <token>'
```

For detailed configuration guidance, see Deploying models by using the MLServer runtime.

#### 4.4.11 Additional Resources

- Caikit API documentation
- OpenVINO KServe-compatible REST API documentation
- OpenAI API documentation
- Open Inference Protocol
- Supported model-serving runtimes
