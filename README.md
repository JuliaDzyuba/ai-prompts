# AI-PROMPTS

| NAME       | PROMPT | DESCRIPTION | EXAMPLE |
|------------|--------|-------------|---------|
| app.yaml  | Generate a Kubernetes Pod manifest with name `app`, labels `app: demo`, `run: demo`, container image `gcr.io/k8s-k3s/demo:v1.0.0`, and expose port `8000`. | Generates a basic Pod manifest with labels and a container. | [app.yaml](./yaml/app.yaml) |
| app-livenessProbe.yaml | Generate a Kubernetes Pod yaml manifest with a liveness probe with name ‘app-livenessprob’ and namespace: demo, use the container image gcr.io/smartcity-gl/demo:v1.0.1, container name `app`, on port 8000, add a liveness probe: check `/health` via HTTP, use port `http`, set initialDelaySeconds: 3, set periodSeconds: 10, timeoutSeconds: 1, failureThreshold: 3 | Generate a Kubernetes Pod manifest with a liveness probe checking `/health` via HTTP every 5 seconds after an initial delay of 3 seconds. | [app-livenessProbe.yaml](./yaml/app-livenessProbe.yaml) |
| app-readinessProbe.yaml | Generate a Kubernetes Pod yaml manifest with both liveness and readiness probes, use name ‘app-readinessprob, use the container image `gcr.io/smartcity-gl/harmonic:v2.0.2`, set container name `app`, expose port `8000`,  add a liveness probe:  check `/` via HTTP, on  port 8000,   set initialDelaySeconds: 5,timeoutSeconds: 1,periodSeconds: 10,failureThreshold: 3;  add a readiness probe:  check `/ready` via HTTP on port 8000, set periodSeconds: 2, initialDelaySeconds: 0, failureThreshold: 3,successThreshold: 1 | Generate a Kubernetes Pod manifest with both liveness and readiness probes. Liveness probe checks `/` every 10 seconds, readiness probe checks `/ready` every 2 seconds. | [app-readinessProbe.yaml](./yaml/app-readinessProbe.yaml) |
| app-volumeMounts.yaml | Create a Kubernetes Pod yaml manifest named app-volume, use container image gcr.io/kuar-demo/kuard-amd64:1 named app, exposing port 8080, including a livenessProbe checking /healthy with initialDelaySeconds: 5,timeoutSeconds: 1,periodSeconds: 10, failureThreshold: 3, a readinessProbe checking /ready with periodSeconds: 2, initialDelaySeconds: 0, failureThreshold: 3, successThreshold: 1, using volumeMounts named data to mount /data, and a volume named data mapped to hostPath /var/lib/app | Mounts a volume named data at /data, which is backed by a hostPath located at /var/lib/app, allowing the container to persist data on the host machine | [app-volumeMounts.yaml](./yaml/app-volumeMounts.yaml) |
| app-cronjob.yaml | Create a Kubernetes CronJob manifest named app-cronjob, scheduled to run every 5 minutes, using a container named hello based on the bash image, executing the command echo "Hello world", with a restart policy set to OnFailure | Schedules a job to run every 5 minutes, executing a simple bash command that prints "Hello world". The container runs in a temporary pod and follows an OnFailure restart policy, ensuring retries if the command fails | [app-cronjob.yaml](./yaml/app-cronjob.yaml) |
| app-job.yaml | Create a Kubernetes Job yaml manifest named app-job, using a Google Cloud Persistent Disk volume for input data and an emptyDir volume for output, with an init container that syncs data from gs://glow-sportradar/ using gsutil, a main container named processor  running glowtools/soccer:latest-4e4254a to process files, lifecycle hooks for logging start and stop events, a restart policy set to Never, and a backoff limit of 0 | Defines a data processing workflow using an init container to fetch input data from Google Cloud Storage, a main container to process the files, and lifecycle hooks to log execution events. It ensures data persistence using a Google Cloud Persistent Disk and temporary storage with an emptyDir volume, while preventing automatic retries with a backoff limit of 0 | [app-job.yaml](./yaml/app-job.yaml) |
| app-multicontainer.yaml | Create a Kubernetes Pod yaml manifest named app-two-containers, with two containers 1st and 2nd sharing an emptyDir volume named html, where the first container runs Nginx and serves files from /usr/share/nginx/html, and the second container runs Debian, continuously appending the current date to /html/index.html every second | Defines a multi-container setup where an Nginx server serves files from a shared volume, while a Debian-based container continuously updates index.html with the current date. The emptyDir volume ensures temporary storage for both containers, enabling dynamic content updates visible via Nginx. | [app-multicontainer.yaml](./yaml/app-multicontainer.yaml) |
| app-resources.yaml | Create a Kubernetes Pod yaml manifest named app-resource, with a container named app running the image gcr.io/kuar-demo/kuard-amd64:1, exposing port 8080, including a liveness probe checking /healthy every 10 seconds after a 5-second delay and timeoutSeconds: 1 and failureThreshold: 3, a readiness probe checking /ready every 2 seconds and initialDelaySeconds:0, failureThreshold: 3, successThreshold: 1, and resource requests of 100m CPU and 128Mi memory, with limits set to 1000m CPU and 256Mi memory | Defines a multi-container setup where an Nginx server serves files from a shared volume, while a Debian-based container continuously updates index.html with the current date. The emptyDir volume ensures temporary storage for both containers, enabling dynamic content updates visible via Nginx. | [app-resources.yaml](./yaml/app-resources.yaml) |
| app-secret-env.yaml | Generate a Kubernetes YAML manifest named app-secret-env for a Pod with container named mycontainer that runs a Redis container and securely injects SECRET_USERNAME and SECRET_PASSWORD from a Kubernetes Secret into environment variables, a restart policy set to Never | Securely injects  SECRET_USERNAME  and  SECRET_PASSWORD  from a Kubernetes Secret into environment variables | [app-secret-env.yaml](./yaml/app-secret-env.yaml) |





# kubectl-ai

`kubectl-ai` acts as an intelligent interface, translating user intent into
precise Kubernetes operations, making Kubernetes management more accessible and
efficient.

## Quick Start

First, ensure that kubectl is installed and configured.

### Installation

#### Quick Install (Linux & MacOS only)

```shell
curl -sSL https://raw.githubusercontent.com/GoogleCloudPlatform/kubectl-ai/main/install.sh | bash
```

### Usage

`kubectl-ai` supports AI models from `gemini`, `vertexai`, `azopenai`, `openai`, `grok` and local LLM providers such as `ollama` and `llama.cpp`.

#### Using Gemini (Default)

Set your Gemini API key as an environment variable. If you don't have a key, get one from [Google AI Studio](https://aistudio.google.com).

```bash
export GEMINI_API_KEY=your_api_key_here
kubectl-ai

# Use different gemini model
kubectl-ai --model gemini-2.5-flash-preview-05-20

# Use 2.5 flash (faster) model
kubectl-ai --quiet --model gemini-2.5-flash-preview-04-17 "check logs for nginx app in hello namespace"
```

Run interactively:

```shell
kubectl-ai
```

The interactive mode allows you to have a chat with `kubectl-ai`, asking multiple questions in sequence while maintaining context from previous interactions. Simply type your queries and press Enter to receive responses. To exit the interactive shell, type `exit` or press Ctrl+C.

Or, run with a task as input:

```shell
kubectl-ai --quiet "fetch logs for nginx app in hello namespace"
```

Combine it with other unix commands:

```shell
kubectl-ai < query.txt
# OR
echo "list pods in the default namespace" | kubectl-ai
```

You can even combine a positional argument with stdin input. The positional argument will be used as a prefix to the stdin content:

```shell
cat error.log | kubectl-ai "explain the error"
```

---

*Note: This is not an officially supported Google product. This project is not
eligible for the [Google Open Source Software Vulnerability Rewards
Program](https://bughunters.google.com/open-source-security).*
