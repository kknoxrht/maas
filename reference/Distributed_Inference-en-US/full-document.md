---
title: "Deploy Models Using Distributed Inference with llm-d on Red Hat OpenShift AI Self-Managed 3.4"
source: "reference/Red_Hat_OpenShift_AI_Self-Managed-3.4-Deploy_models_using_Distributed_Inference_with_llm-d-en-US.pdf"
conversion_date: "2026-04-24"
conversion_method: "claude-optimized"
product: "Red Hat OpenShift AI"
version: "3.4"
document_type: "technical documentation"
topics:
  - distributed inference
  - llm-d framework
  - large language model deployment
  - vLLM runtime
  - autoscaling
  - workload variant autoscaler
  - scheduler configuration
  - KServe integration
  - authentication
  - rate limiting
  - multi-node deployment
last_updated: "2026-04-23"
---

# Deploy Models Using Distributed Inference with llm-d

**Last Updated:** 2026-04-23

## Abstract

As an administrator, you can use distributed inference to deploy and serve large language models at scale on Red Hat OpenShift AI.

---

## Preface

As an administrator, you can use Distributed Inference with llm-d to deploy and serve large language models at scale on Red Hat OpenShift AI. You can configure distributed inference and set up authentication and authorization for your model deployments.

---

## Chapter 1: Deploying Models by Using Distributed Inference with LLM-D

Distributed Inference with llm-d is a Kubernetes-native, open-source framework designed for serving large language models (LLMs) at scale. You can use Distributed Inference with llm-d to simplify the deployment of generative AI, focusing on high performance and cost-effectiveness across various hardware accelerators.

### Key Features

- Efficiently handles large models using optimizations such as prefix-cache aware routing and disaggregated serving
- Integrates into a standard Kubernetes environment, leveraging specialized components like the Envoy proxy for networking and routing, and high-performance libraries such as vLLM and NVIDIA Inference Transfer Library (NIXL)
- Tested recipes and well-known presets reduce the complexity of deploying inference at scale, allowing users to focus on building applications rather than managing infrastructure

### 1.1 Enabling Distributed Inference with LLM-D

This procedure describes how to create a custom resource (CR) for an LLMInferenceService resource. You replace the default InferenceService with the LLMInferenceService.

#### Prerequisites

- You have enabled the model serving platform
- You have access to an OpenShift cluster running version 4.19.9 or later
- OpenShift Service Mesh v2 is not installed in the cluster
- Your cluster administrator has created a GatewayClass and a Gateway named `openshift-ai-inference` in the `openshift-ingress` namespace as described in Gateway API with OpenShift Container Platform Networking

**IMPORTANT:** Review the Gateway API deployment topologies. Only use shared Gateways across trusted namespaces.

- Your cluster administrator has installed the LeaderWorkerSet Operator in OpenShift
- If you are running OpenShift on a bare-metal cluster, your cluster administrator has an external entry point for the openshift-ai-inference Gateway service

**NOTE:** By default, the Inference Gateway uses `type: LoadBalancer`. If the cluster does not already include support for LoadBalancer services, you can use the OpenShift option described in Load balancing with MetalLB.

- You have enabled authentication as described in Configuring authentication for Distributed Inference with llm-d

#### Procedure

1. Log in to the OpenShift console as a developer

2. Create the LLMInferenceService CR with the following information:

```yaml
# LLMInferenceService deployment
# Context: Deploy RedHatAI Qwen model with distributed inference
# Prerequisites: Gateway and authentication configured
apiVersion: serving.kserve.io/v1alpha1
kind: LLMInferenceService
metadata:
  name: sample-llm-inference-service
spec:
  replicas: 2
  model:
    uri: hf://RedHatAI/Qwen3-8B-FP8-dynamic
    name: RedHatAI/Qwen3-8B-FP8-dynamic
  router:
    route: {}
    gateway: {}
    scheduler: {}
  template:
    containers:
    - name: main
      resources:
        limits:
          cpu: '4'
          memory: 32Gi
          nvidia.com/gpu: "1"
        requests:
          cpu: '2'
          memory: 16Gi
          nvidia.com/gpu: "1"
```

Customize the following parameters in the spec section:

- **replicas**: Specify the number of replicas
- **model**: Specify the URI to the model based on how the model is stored (uri) and the model name to use in chat completion requests (name)
  - S3 bucket: `s3://<bucket-name>/<object-key>`
  - Persistent volume claim (PVC): `pvc://<claim-name>/<pvc-path>`
  - OCI container image: `oci://<registry_host>/<org_or_username>/<repository_name>:<tag_or_digest>`
  - HuggingFace: `hf://<model>/<optional-hash>`
- **router**: Provide an HTTPRoute and gateway, or leave blank to automatically create one

3. Save the file

---

## Chapter 2: vLLM Arguments Reference

When you deploy models using Distributed Inference with llm-d, the vLLM runtime handles inference requests. Red Hat AI Inference Server provides comprehensive documentation for vLLM runtime arguments and advanced configuration that you can apply to your llm-d deployments.

### 2.1 Common Use Cases for vLLM Arguments

Configure vLLM runtime arguments to achieve the following goals:

#### Optimize Resource Usage

Reduce GPU memory consumption to fit larger models or enable more concurrent requests. For example:
- Using `--gpu-memory-utilization=0.85` leaves headroom for system operations and prevents out-of-memory errors
- Adjust context length limits to match your application's requirements using `--max-model-len=8192` for smaller context length to reduce memory requirements

#### Improve Performance

- Minimize latency in production by disabling verbose logging using `--disable-uvicorn-access-log` to reduce I/O overhead from logging every HTTP request
- Enable faster text generation using speculative decoding with `--speculative-model=<draft-model>` and `--use-v2-block-manager` to use a smaller draft model to predict tokens, which is verified by the main model

#### Support Specific Model Requirements

- Enable tool calling with custom models using `--enable-auto-tool-choice` and `--tool-call-parser=hermes` to enable the model to select tools automatically and parse tool calls using model-specific formats
- Support custom chat templates for models that require specific formatting
- Enable prefix caching to speed up requests with repeated prompts using `--enable-prefix-caching`

#### Meet Operational Needs

- Configure trust settings for custom or private model sources using `--trust-remote-code`
- Reduce log volume in production environments by disabling access logs

### 2.2 Argument Reference

For complete documentation, see:
- vLLM Server Arguments
- Advanced features
- Extending Red Hat AI Inference Server with Tool Calling Capabilities
- Using custom chat templates with models

### 2.3 Configure vLLM Arguments for LLM-D Deployments

You can configure vLLM runtime arguments to optimize inference performance by using the standard Kubernetes container args field to control memory allocation, request handling, and model behavior without redeploying or rebuilding containers.

#### Prerequisites

- You have OpenShift AI 3.4 EA2 or later installed
- You have deployed a model using Distributed Inference with llm-d for distributed inference or single-GPU deployments
- You have stored a model in S3, a persistent volume claim (PVC), an OCI container registry, or HuggingFace
- You have access to the OpenShift CLI (oc) or the OpenShift web console

#### Procedure

1. Identify the vLLM arguments you want to configure. For the complete argument reference, see Red Hat AI Inference Server vLLM Server Arguments.

2. Create or edit the LLMInferenceService custom resource to include the args field under spec.template.containers for the container named main:

```yaml
# vLLM argument configuration
# Context: Optimize inference performance with custom vLLM settings
# Prerequisites: Model deployed with llm-d
apiVersion: serving.kserve.io/v1alpha2
kind: LLMInferenceService
metadata:
  name: my-vllm-service
  namespace: <my_namespace>
spec:
  replicas: 2
  model:
    uri: hf://RedHatAI/Qwen3-8B-FP8-dynamic
    name: RedHatAI/Qwen3-8B-FP8-dynamic
  router:
    route: {}
    gateway: {}
    scheduler: {}
  template:
    containers:
    - name: main
      args:
      - --disable-uvicorn-access-log
      - --max-model-len=10000
      resources:
        limits:
          cpu: '4'
          memory: 32Gi
          nvidia.com/gpu: "1"
        requests:
          cpu: '2'
          memory: 16Gi
          nvidia.com/gpu: "1"
```

Where:
- `--disable-uvicorn-access-log`: Disables HTTP access logs to reduce log volume
- `--max-model-len=10000`: Sets the maximum context length to 10,000 tokens

**NOTE:** The arguments that you specify are merged with the default arguments provided by the system. If you specify the same argument as a default, your value takes precedence. This allows you to override specific defaults without affecting other default arguments.

3. Apply the custom resource:

```bash
# Apply LLMInferenceService configuration
# Context: Deploy custom vLLM arguments to namespace
oc apply -f llminferenceservice.yaml -n <my_namespace>
```

Example output:
```
llminferenceservice.serving.kserve.io/my-vllm-service configured
```

#### Verification

1. Verify that the arguments are applied correctly:

   a. Check that the LLMInferenceService is running:

```bash
# Check LLMInferenceService status
oc get llminferenceservice my-vllm-service -n <my_namespace>
```

Example output:
```
NAME              READY   AGE
my-vllm-service   True    5m
```

   b. The READY column shows True. Verify your custom arguments appear in the pod specification:

Get the pod name:

```bash
# Get pods for the inference service
oc get pods -n <my_namespace> -l app.kubernetes.io/name=my-vllm-service
```

Inspect the container arguments:

```bash
# Inspect pod arguments
oc describe pod <pod_name> -n <my_namespace> | grep -A 5 "Args:"
```

Example output:
```
Args:
  --disable-uvicorn-access-log
  --max-model-len=10000
```

   c. Your custom arguments should appear in this list. Test that inference requests work with your configured arguments:

Get the route URL:

```bash
# Get inference service route URL
oc get llmisvc -n <my_namespace> my-vllm-service -o jsonpath='{.status.url}'
```

Send a test request:

```bash
# Test inference request with authentication
# Context: Verify vLLM service is running with configured arguments
curl -X POST https://<route_url>/v1/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $(oc whoami -t)" \
  -d '{"model": "RedHatAI/Qwen3-8B-FP8-dynamic", "prompt": "Explain what Red Hat OpenShift AI is in one sentence.", "max_tokens": 100}'
```

A successful response confirms that your vLLM service is running with the configured arguments.

#### Additional Resources

- Red Hat AI Inference Server vLLM Server Arguments
- Using custom chat templates with models

### 2.4 Configuring Authentication for Distributed Inference with LLM-D Using Red Hat Connectivity Link

Red Hat Connectivity Link provides Kubernetes-native authentication and authorization capabilities for Distributed Inference with llm-d inference endpoints. Connectivity Link works with the gateway to intercept incoming traffic before it reaches the vLLM inference service, validating the requests based on authentication tokens and authorization policies.

For more information about Connectivity Link concepts and capabilities, see Introduction to Connectivity Link.

#### Prerequisites

- You have installed Red Hat Connectivity Link version 1.1.1 or later
- You have access to the OpenShift CLI (oc)
- The ServiceAccount has permission to get the corresponding LLMInferenceService and you have generated a JSON web token (JWT)

#### Procedure

1. Create the Kuadrant custom resource (CR) to set up required objects:

```bash
# Create Kuadrant CR
# Context: Enable authentication and authorization for llm-d
oc apply -f - <<EOF
apiVersion: kuadrant.io/v1beta1
kind: Kuadrant
metadata:
  name: kuadrant
  namespace: kuadrant-system
EOF
```

2. Wait for Kuadrant to become ready:

```bash
# Wait for Kuadrant readiness
oc wait Kuadrant -n kuadrant-system kuadrant --for=condition=Ready --timeout=10m
```

3. Add the ServingCert annotation to the Authorino Service:

```bash
# Add serving certificate annotation
# Context: Enable TLS for Authorino service
oc annotate svc/authorino-authorino-authorization service.beta.openshift.io/serving-cert-secret-name=authorino-server-cert -n kuadrant-system
```

4. Wait for the secret to be created:

```bash
sleep 2
```

5. Update Authorino to enable SSL:

```bash
# Update Authorino with SSL configuration
# Context: Enable TLS for authentication
oc apply -f - <<EOF
apiVersion: operator.authorino.kuadrant.io/v1beta1
kind: Authorino
metadata:
  name: authorino
  namespace: kuadrant-system
spec:
  replicas: 1
  clusterWide: true
  listener:
    tls:
      enabled: true
      certSecretRef:
        name: authorino-server-cert
  oidcServer:
    tls:
      enabled: false
EOF
```

6. Verify that the Authorino pods are ready:

```bash
# Verify Authorino pod status
oc wait --for=condition=ready pod -l authorino-resource=authorino -n kuadrant-system --timeout 150s
```

7. If OpenShift AI was installed before installing Connectivity Link and Kuadrant, restart the controllers:

```bash
# Restart OpenShift AI controllers
# Context: Refresh controllers after Connectivity Link installation
oc delete pod -n redhat-ods-applications -l app=odh-model-controller
oc delete pod -n redhat-ods-applications -l control-plane=kserve-controller-manager
```

### 2.5 Enabling Authentication and Authorization for an LLM Inference Service

In OpenShift AI 3.0 and later, authentication and authorization are automatically enabled for LLMInferenceService resources when Red Hat Connectivity Link is configured. You can use the `security.opendatahub.io/enable-auth: "true"` annotation to explicitly enable authentication, such as re-enabling it after it was previously disabled.

#### Prerequisites

- You have configured Red Hat Connectivity Link for Distributed Inference with llm-d
- You have created an LLMInferenceService resource as described in Enabling Distributed Inference with llm-d
- You have access to the OpenShift CLI (oc)

#### Procedure

1. By default, authentication is enabled automatically. To explicitly enable authentication or to re-enable it after disabling, annotate your LLMInferenceService resource:

```yaml
# Enable authentication for LLM inference service
# Context: Secure model access with Red Hat Connectivity Link
# Prerequisites: Connectivity Link configured
apiVersion: serving.kserve.io/v1alpha1
kind: LLMInferenceService
metadata:
  name: sample-llm-inference-service
  annotations:
    security.opendatahub.io/enable-auth: "true"
spec:
  replicas: 2
  model:
    uri: hf://RedHatAI/Qwen3-8B-FP8-dynamic
    name: RedHatAI/Qwen3-8B-FP8-dynamic
  router:
    route: {}
    gateway: {}
    scheduler: {}
  template:
    containers:
    - name: main
      resources:
        limits:
          cpu: '4'
          memory: 32Gi
          nvidia.com/gpu: "1"
        requests:
          cpu: '2'
          memory: 16Gi
          nvidia.com/gpu: "1"
```

2. Apply the configuration:

```bash
# Apply LLMInferenceService with authentication enabled
oc apply -f <llm-inference-service-file>.yaml
```

#### Verification

Confirm that the LLMInferenceService resource has the annotation:

```bash
# Verify authentication annotation
oc get llminferenceservice sample-llm-inference-service -o jsonpath='{.metadata.annotations.security\.opendatahub\.io/enable-auth}'
```

The command returns `true`.

Verify that the inference service is protected by attempting to access it without authentication:

```bash
# Test unauthenticated access (should fail)
curl -v https://<inference-endpoint-url>/v1/models
```

The request returns a 401 Unauthorized response, confirming that unauthenticated requests are rejected.

#### Additional Resources

Override your Gateway policies for auth and rate limiting

### 2.6 Example Usage for Distributed Inference with LLM-D

These examples show how to use Distributed Inference with llm-d in common scenarios.

#### 2.6.1 Single-node GPU Deployment

Use single-GPU-per-replica deployment patterns for development, testing, or production deployments of smaller models, such as 7-billion-parameter models.

For examples using single-node GPU deployments, see Single-Node GPU Deployment Examples.

#### 2.6.2 Multi-node Deployment

For examples using multi-node deployments, see DeepSeek-R1 Multi-Node Deployment Examples.

#### 2.6.3 Intelligent Inference Scheduler with KV Cache Routing

You can configure the scheduler to track key-value (KV) cache blocks across inference endpoints and route requests to the endpoint with the highest cache hit rate. This configuration improves throughput and reduces latency by maximizing cache reuse.

For an example, see Precise Prefix KV Cache Routing.

### 2.7 Making Authenticated Inference Requests to Distributed Inference with LLM-D

When you enable authentication for an LLMInferenceService, you must include a valid JSON web token (JWT) in your inference requests. You can generate a token from a ServiceAccount or use an OIDC token from an identity provider.

#### Prerequisites

- You have enabled authentication for your LLMInferenceService
- You have access to the OpenShift CLI (oc)
- You have the inference endpoint URL for your deployed model

#### Procedure

1. Create a ServiceAccount with permissions to access the LLMInferenceService:

```bash
# Create service account for LLM access
# Context: Set up authentication for inference requests
oc create serviceaccount llm-user -n <namespace>
```

2. Create a Role that grants permission to get the LLMInferenceService:

```bash
# Create role for LLM inference access
# Context: Grant read permissions to LLMInferenceService
oc apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: llm-inference-viewer
  namespace: <namespace>
rules:
- apiGroups: ["serving.kserve.io"]
  resources: ["llminferenceservices"]
  verbs: ["get"]
  resourceNames: ["<llm-inference-service-name>"]
EOF
```

3. Bind the Role to the ServiceAccount:

```bash
# Bind role to service account
# Context: Grant service account LLM access permissions
oc create rolebinding llm-user-binding \
  --role=llm-inference-viewer \
  --serviceaccount=<namespace>:llm-user \
  -n <namespace>
```

4. Generate a JWT token from the ServiceAccount:

```bash
# Generate JWT token for authentication
# Context: Create temporary token for inference requests
TOKEN=$(oc create token llm-user -n <namespace> --duration=1h)
```

The `--duration` flag sets the token validity period. Adjust this value based on your workload requirements. For production use, consider using a shorter duration or integrating with an OIDC provider.

#### Verification

Verify that the authenticated request was successful:

```bash
# Test authenticated chat completion request
# Context: Verify authentication is working correctly
curl -v https://<inference-endpoint-url>/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer ${TOKEN}" \
  -d '{
    "model": "<model-name>",
    "messages": [{
      "role": "user",
      "content": "What is Red Hat OpenShift AI?"
    }]
  }'
```

A successful response indicates that authentication is working correctly.

Verify that requests without authentication are rejected:

```bash
# Test unauthenticated request (should fail)
curl -v https://<inference-endpoint-url>/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "<model-name>",
    "messages": [{
      "role": "user",
      "content": "What is Red Hat OpenShift AI?"
    }]
  }'
```

The request returns a 401 Unauthorized response with a message indicating that authentication is required.

Verify that requests with an invalid or expired token are rejected:

```bash
# Test with invalid token (should fail)
curl -v https://<inference-endpoint-url>/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer invalid-token" \
  -d '{
    "model": "<model-name>",
    "messages": [{
      "role": "user",
      "content": "What is Red Hat OpenShift AI?"
    }]
  }'
```

The request returns a 401 Unauthorized response, confirming that invalid tokens are rejected.

#### Additional Resources

- For information about OpenShift authentication, see Understanding authentication
- For information about ServiceAccount tokens, see Using bound service account tokens

---

## Chapter 3: Automatically Scale LLM-D Model Deployments

You can configure intelligent, inference-aware automatic scaling for llm-d model deployments using the workload variant autoscaler (WVA). WVA automatically adjusts replica counts and AI accelerator allocation based on real-time traffic signals and hardware capacity, helping you optimize infrastructure usage while maintaining inference performance.

**IMPORTANT:** Workload variant autoscaler is a Developer Preview feature only. Developer Preview features are not supported by Red Hat in any way and are not functionally complete or production-ready. Do not use Developer Preview features for production or business-critical workloads.

### 3.1 Workload Variant Autoscaler Overview

The workload variant autoscaler (WVA) is a Kubernetes controller that provides intelligent, inference-aware autoscaling for llm-d model deployments. You can use the WVA to automatically adjust replica counts and AI accelerator allocation based on real-time traffic signals, hardware capacity, and workload characteristics instead of relying on generic metrics such as CPU or memory utilization.

Traditional Kubernetes autoscaling approaches rely on metrics such as CPU utilization, memory consumption, or queries per second (QPS). These metrics do not accurately represent the resource demands of large language model (LLM) inference workloads because LLM inference is GPU-bound, not CPU-bound.

The WVA addresses these limitations by deriving scaling decisions from inference-specific signals that directly reflect the state of the model server.

The WVA operates as part of the llm-d inference stack alongside the following components:

- **vLLM model servers**: The inference serving backends that the WVA scales
- **Inference Gateway**: Routes incoming requests to available model server replicas
- **Inference scheduler**: Selects optimal endpoints for each request based on load and locality
- **KV cache management**: Manages key-value cache storage and transfer between replicas (embedded in the inference scheduler)
- **KEDA**: Provides the autoscaling actuator that the WVA uses to manage HPA resources

### 3.2 Key Metrics for Autoscaling Decisions

The workload variant autoscaler (WVA) uses the following inference-specific metrics from vLLM model servers to drive scaling decisions:

- **KV cache utilization** (`kserve_vllm:kv_cache_usage_perc`): The percentage of key-value cache in use on each replica. The WVA queries the maximum value over a 1-minute window. A replica is considered saturated when its KV cache utilization reaches the configured kvCacheThreshold.

- **Queue length** (`kserve_vllm:num_requests_waiting`): The number of requests waiting to be processed on each replica. The WVA queries the maximum value over a 1-minute window. A replica is considered saturated when its queue length reaches the configured queueLengthThreshold.

The WVA evaluates these metrics across all replicas of a deployment to calculate spare capacity and determine whether to scale up or scale down.

### 3.3 How the Workload Variant Autoscaler Works

The workload variant autoscaler (WVA) is deployed as a controller that monitors VariantAutoscaling custom resources and adjusts replica counts for model server deployments. To deploy the WVA controller, you must enable it in the DataScienceCluster custom resource.

There is a single inference stack per namespace. You cannot deploy multiple inference stacks in the same namespace.

You can create multiple LLMInferenceService CRs within a single stack to serve the same model on different accelerator types. For example, you might have one deployment on H100 GPUs and another on A100 GPUs. The WVA coordinates scaling across all variants that share the same model identifier, scaling up the cheapest variant first.

The WVA uses a saturation-based spare capacity model to make scaling decisions. It evaluates three main dimensions:

#### Token-level Demand Estimation

By measuring KV cache utilization as a proxy for tokens actively being processed, and the number of requests waiting in queue, the WVA computes token-level demand rather than relying on generic throughput metrics such as CPU or memory utilization.

#### Saturation-aware Spare Capacity

The WVA compares token-level demand against each replica's effective capacity: the minimum of its memory-bound KV cache size limit and compute-bound limit, or batch saturation. Scaling decisions are triggered when spare capacity across replicas falls below a threshold, rather than waiting for replicas to become fully saturated.

#### Hardware-aware Optimization

At a per-variant level, the WVA accounts for AI accelerator saturation levels. In the LLMISVC CRD, different accelerators can be selected for the requirements of each variant, for example:
- Prefill optimized
- Generic inference workers capable of both workloads
- Other configurations that can change the model server's behavior

#### 3.3.1 Saturation Scaling Algorithm

The workload variant autoscaler (WVA) uses a saturation-based spare capacity model to make scaling decisions. The algorithm evaluates whether the current replica set has sufficient spare capacity to absorb traffic increases.

The scaling algorithm works as follows:

1. **Identify non-saturated replicas**: A replica is non-saturated if its KV cache utilization is below kvCacheThreshold and its queue length is below queueLengthThreshold.

2. **Calculate spare capacity**: For each non-saturated replica, the WVA calculates the spare KV capacity and spare queue capacity.

3. **Average spare capacity**: The WVA averages the spare capacity across all non-saturated replicas.

4. **Scale-up decision**: The WVA signals a scale-up if the average spare KV capacity is below kvSpareTrigger or the average spare queue capacity is below queueSpareTrigger.

5. **Scale-down safety check**: The WVA simulates removing one replica and redistributing load. Scale-down is safe only if remaining spare capacity still exceeds the triggers and at least 2 non-saturated replicas exist.

A variant is eligible for scaling if it does not already have a pending scaling decision from a previous reconciliation cycle. If a variant's desired replica count differs from its current count, the WVA preserves the existing decision and skips that variant when selecting candidates for new scaling actions.

To prevent over-provisioning during the model loading period, the WVA skips variants that have pending replicas when selecting a variant to scale up. A replica is considered pending when the pod exists but is not yet reporting metrics. This cascade scaling prevention ensures that only variants with all replicas ready are eligible for additional scale-up.

---

## Chapter 4: Configuring Scheduler Settings for LLM Inference Services

You can configure custom scheduler settings for large language model (LLM) inference services to optimize request routing and improve performance characteristics such as cache efficiency and load distribution.

### 4.1 Configure Scheduler Settings for LLM Inference Services

You can configure custom scheduler settings for large language model (LLM) inference services to optimize request routing and improve performance characteristics, such as prefix cache hit rates and request distribution patterns.

The EndpointPicker determines how incoming requests are routed to available model endpoints in your LLM inference service deployment. When multiple replicas of a model are running, the scheduler decides which specific replica handles each request.

If you do not specify custom scheduler configuration, the system uses a default profile that balances requests across replicas by combining queue depth (with a weight value of 2) and prefix cache hit scoring (with a weight value of 3), then selects the highest-scoring replica. This default prioritizes KV cache reuse over even load distribution to minimize redundant computation.

#### 4.1.1 Configuration Methods

You can configure scheduler settings using two methods:

**Inline configuration**: Embeds the scheduler settings directly in the LLMInferenceService resource specification. This method is suitable for simple configurations or when you have unique scheduler settings for a specific service. It keeps all settings for a service in one place for easy viewing.

**ConfigMap-based configuration**: Stores scheduler settings in a Kubernetes ConfigMap and references it from the LLMInferenceService resource. This method is ideal when you want to reuse the same scheduler configuration across multiple LLM inference services, or when you prefer to manage configurations separately from service definitions. It also enables centralized configuration management and easier updates to multiple services.

The two configuration methods are mutually exclusive. You cannot use both inline and ConfigMap-based configuration in the same LLMInferenceService resource.

### 4.2 Configuring Scheduler Inline in LLMInferenceService

You can configure scheduler settings directly within your LLMInferenceService resource specification by using inline configuration. This approach embeds the scheduler configuration in the same YAML file as your service definition, making it suitable for service-specific settings.

#### Prerequisites

- You have cluster administrator privileges or namespace administrator privileges for the namespace where you want to deploy the LLM inference service
- You have installed OpenShift AI and configured model serving
- You have a model that you want to deploy as an LLM inference service, or you have an existing LLMInferenceService resource that you want to update

#### Procedure

1. Create a YAML file for your LLMInferenceService resource or open an existing service definition for editing.

2. Add the router.scheduler.config.inline section to the service specification:

```yaml
# LLM inference service with inline scheduler configuration
# Context: Deploy model with custom endpoint picker settings
# Prerequisites: Model available, namespace created
apiVersion: serving.kserve.io/v1alpha1
kind: LLMInferenceService
metadata:
  name: llm-service-with-scheduler
  namespace: <your_namespace>
spec:
  model:
    uri: hf://<your_model_uri>
    name: <your_model_name>
  router:
    scheduler:
      config:
        inline:
          apiVersion: inference.networking.x-k8s.io/v1alpha1
          kind: EndpointPickerConfig
          plugins:
          - type: prefix-cache-scorer
          - type: queue-scorer
          schedulingProfiles:
          - name: default
            plugins:
            - pluginRef: queue-scorer
              weight: 2
            - pluginRef: prefix-cache-scorer
              weight: 3
```

3. Apply the configuration to your cluster:

```bash
# Apply LLMInferenceService with inline scheduler configuration
oc apply -f llm-service.yaml
```

4. Verify that the LLM inference service is running with the custom scheduler configuration:

```bash
# Verify LLM inference service status
oc get llminferenceservice llm-service-optimized -n model-serving
```

The output shows the service status. Wait until the service reaches the Ready state.

#### Verification

Send test requests to your LLM inference service to verify that it is handling requests correctly with the configured scheduler settings.

### 4.3 Configuring Scheduler Using ConfigMap References

You can configure scheduler settings by storing the configuration in a Kubernetes ConfigMap and referencing it from your LLMInferenceService resource. This approach is ideal for sharing configurations across multiple services or managing configurations separately from service definitions.

#### Prerequisites

- You have cluster administrator privileges or namespace administrator privileges for the namespace where you want to deploy the LLM inference service
- You have installed OpenShift AI and configured model serving
- You have a model that you want to deploy as an LLM inference service, or you have an existing LLMInferenceService resource that you want to update
- You are familiar with Kubernetes ConfigMaps

#### Procedure

1. Create a ConfigMap that contains your scheduler configuration:

```yaml
# Scheduler configuration ConfigMap
# Context: Centralized scheduler settings for multiple LLM services
apiVersion: v1
kind: ConfigMap
metadata:
  name: llm-scheduler-config
  namespace: <your_namespace>
data:
  default-scheduler: |
    apiVersion: serving.kserve.io/v1alpha1
    kind: EndpointPickerConfig
    plugins:
    - type: prefix-cache-scorer
    - type: queue-scorer
    schedulingProfiles:
    - name: default
      plugins:
      - pluginRef: queue-scorer
        weight: 2
      - pluginRef: prefix-cache-scorer
        weight: 3
```

2. Apply the ConfigMap to your cluster:

```bash
# Apply scheduler configuration ConfigMap
oc apply -f scheduler-config.yaml
```

3. Verify that the ConfigMap was created successfully:

```bash
# Verify ConfigMap creation
oc get configmap llm-scheduler-config -n <your_namespace>
```

4. Create or edit your LLMInferenceService resource to reference the ConfigMap:

```yaml
# LLM inference service with ConfigMap-based scheduler configuration
# Context: Deploy model using centralized scheduler settings
# Prerequisites: Scheduler ConfigMap created
apiVersion: serving.kserve.io/v1alpha1
kind: LLMInferenceService
metadata:
  name: llm-service-with-configmap
  namespace: <your_namespace>
spec:
  model:
    uri: hf://<your_model_uri>
    name: <your_model_name>
  router:
    scheduler:
      config:
        ref:
          name: llm-scheduler-config
          key: default-scheduler
```

5. Apply the LLMInferenceService configuration:

```bash
# Apply LLMInferenceService with ConfigMap reference
oc apply -f llm-service.yaml
```

6. Verify that the LLM inference service is running with the scheduler configuration from the ConfigMap:

```bash
# Verify service status
oc get llminferenceservice llm-service-with-configmap -n <your-namespace>
```

Wait until the service reaches the Ready state.

#### Verification

Send test requests to your LLM inference service to verify that it handles requests correctly with the configured scheduler settings.

### 4.4 Scheduler Plugin Reference

Scheduler plugins implement specific routing logic that determines how the EndpointPicker distributes requests across model replicas. You can configure these plugins in the EndpointPickerConfig to customize request routing behavior.

When multiple plugins are configured, the scheduler combines their scores to make the final routing decision. The scheduler evaluates each plugin for every request and selects the endpoint with the highest combined score.

#### 4.4.1 Default Plugins

**queue-scorer**: The queue-scorer plugin scores replicas based on queue depth. Replicas with shorter queues score higher, helping to distribute requests evenly across available replicas and prevent overloading individual instances.

**prefix-cache-scorer**: The prefix-cache-scorer plugin optimizes request routing for improved prefix cache hit rates. It routes requests with similar prompt prefixes to the same model replica, increasing the likelihood that the replica can reuse previously computed results for common prompt prefixes.
