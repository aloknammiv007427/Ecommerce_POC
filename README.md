# Ecommerce_POC
POC for Ecommerce Dot Net application Build and Deploy

**CI Pipeline (azure-pipelines-ci.yml)**
**1. Trigger**
Automatically triggers the pipeline when changes are pushed to the main or release/* branches.

**2. Pool**
Specifies the agent pool to use. Here, we use a Microsoft-hosted agent with a windows-latest image.

**3. Variables**
buildConfiguration: Defines the build mode as Release.
imageName: Name of the Docker image.
acrName: Name of the Azure Container Registry (ACR).

**4. Tasks**
**Setup .NET Core SDK (UseDotNet@2):**
Ensures the pipeline environment has the required .NET SDK installed.

**Restore Dependencies (DotNetCoreCLI@2):**
Restores NuGet packages for the application.

**Build Application (DotNetCoreCLI@2):**
Compiles the application in the Release mode for optimal performance.

**Run Unit Tests (DotNetCoreCLI@2):**
Executes unit tests to ensure application logic is correct.

**SonarQube Analysis (SonarCloudPrepare, SonarCloudAnalyze, SonarCloudPublish):**
Performs static code analysis to check for code quality and vulnerabilities.

**Publish Artifacts (PublishBuildArtifacts@1):**
Stores build outputs for later use (e.g., debugging, publishing).

**Build and Push Docker Image to ACR (Docker@2):**
Builds the application image using the Dockerfile and pushes it to ACR with a unique tag (Build.BuildId).


**CD Pipeline (azure-pipelines-cd.yml)**
**1. Trigger**
Automatically starts when changes are merged into specific branches like main or release/*.

**2. Pool**
Uses an ubuntu-latest image for Linux-based tasks like interacting with Kubernetes.

**3. Variables**
namespace: Kubernetes namespace for deployment (ecommerce-namespace).
imageName: Docker image name (ecommerce-dotnet-app).
acrName: ACR name for retrieving the image.
replicaCount: Number of pods to run for the application.

**4. Tasks**
**Create Namespace (Kubernetes@1):**
Ensures the target namespace exists in the AKS cluster. If it doesn’t exist, the task creates it using k8s-namespace.yaml.

**Deploy Application to AKS (Kubernetes@1):**
Applies the Kubernetes manifests (e.g., k8s-deployment.yaml) to deploy the application to AKS.

**Check Pod Status (Kubernetes@1):**
Verifies if the pods are running and ready.

**Validate Services (Kubernetes@1):**
Describes the LoadBalancer service to ensure external access to the application.
