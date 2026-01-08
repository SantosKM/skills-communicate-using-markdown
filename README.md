
# c360-cloud-run-migration

> **Cloud Run Migration Toolkit**

## 📋 Description

Automation toolkit facilitating migration of legacy App Engine and Compute Engine services to Cloud Run serverless platform. Provides pre-flight checks, containerization templates, environment configuration mapping, migration planning, traffic shifts, automatic rollback, and reporting.

**Configuration Essentials:**
- Language: Python + Bash scripts
- Target: Google Cloud Run
- Input: Service metadata
- Output: Migration plan + automation scripts
- Rollback: Automatic on errors
- Downtime Target: Zero

## 🎯 Capabilities
- Pre-migration validation
- Dockerfile generation
- Environment mapping
- Configuration conversion
- Traffic migration
- Health check validation
- Automatic rollback
- Migration reporting

## 🏗️ Architecture
```
Legacy App Engine/GCE
Service
        │
        │ Migration analysis
        ▼
c360-cloud-run-migration
├─ Validate compatibility
├─ Generate Dockerfile
├─ Map environments
├─ Create Cloud Run service
└─ Migrate traffic
        │
        ├─ Gradual traffic shift
        ├─ Health monitoring
        └─ Automatic rollback (if needed)
        │
        ▼
Cloud Run Service
(production)
```

## 🔄 Business Flow
### How Migration Works
**Purpose**: Safely move services to serverless platform.

**Migration Workflow:**
1. **Pre-Flight Checks**
    ```bash
    ✓ Service accessible
    ✓ Dependencies available
    ✓ Memory requirements OK
    ✓ Timeout requirements OK
    ```
2. **Generate Dockerfile**
    ```dockerfile
    FROM gcr.io/gke-release/ubuntu:20.04
    COPY app/ /app/
    EXPOSE 8080
    CMD ["python", "-m", "app.main"]
    ```
3. **Map Environment**
    ```
    App Engine  →  Cloud Run
    ENV vars    →  Secret Manager
    Services    →  Private services
    DB  →  Cloud SQL proxy
    ```
4. **Create Cloud Run**
    ```bash
    gcloud run deploy service \
      --image gcr.io/project/service:v1 \
      --memory 512Mi \
      --timeout 3600s
    ```
5. **Migrate Traffic**
    - 10% traffic → Cloud Run
    - Monitor metrics
    - Increment 10% every 5 minutes
    - Rollback on error

**Migration Timeline:**
```
Hour 0:00 - Pre-flight checks
Hour 0:05 - Generate Docker image (10 min)
Hour 0:15 - Build & push (5 min)
Hour 0:20 - Deploy to Cloud Run
Hour 0:25 - Route 10% traffic
Hour 0:30 - Route 20% traffic
Hour 1:00 - Route 100% traffic
Hour 1:05 - Delete old service

Total: 65 minutes, zero downtime
```

**Key Features:**
- ✓ Zero downtime
- ✓ Automatic rollback
- ✓ Gradual traffic shift
- ✓ Pre-flight validation

### Error Handling
Clear, actionable error handling keeps migrations safe and supportable. The table below lists common error conditions and recommended operational steps.

| Error Code / Type | Description | Action / Outcome |
|---|---|---|
| DEPLOYMENT_FAIL | Failure during Cloud Run deployment. | Retry the deployment, surface logs for diagnostics, and rollback if necessary; notify operators and include deployment logs. |
| IAM_MISSING | Required IAM role missing. | Fail fast with clear instructions to grant the required role; log the failure and notify owners. |
| CONFIG_PARSE_ERROR | Invalid migration configuration. | Abort migration and report error to operator with config line/offset and example values for correction. |
| IMAGE_PULL_ERROR | Unable to pull base image. | Verify registry authentication, retry the pull, and alert on persistent failures to registry owners. |
| UNKNOWN_ERROR | Any unexpected exception. | Capture logs and diagnostic outputs; surface to operators for manual troubleshooting and post-mortem. |

## 📦 Dependencies
**Upstream:**
- Legacy service

**Downstream:**
- Cloud Run
- Cloud SQL
- Secret Manager

**Libraries:**
- `google-cloud-run`
- `docker`

## 🛠️ Configuration
```bash
MIGRATION_CONFIG = {
    'traffic_step': 10,  # 10% increments
    'step_duration_minutes': 5,
    'max_memory_mb': 512,
    'max_timeout_seconds': 3600
}
```

## 🚀 Deployment
```bash
./migrate.sh --service myservice --target cloud-run
```

## 📈 Monitoring
### Key Metrics
- `migration_progress_percent` - % traffic moved
- `error_rate_comparison` - Old vs new
- `latency_comparison_ms` - Performance
- `cost_savings_percent` - Cloud Run vs old

### Alerts
- **High Error Rate**: >2x baseline
- **High Latency**: >50% increase
- **Health Check Failures**: >10%

## 📚 Resources
- **GitHub**: `https://github.com/procter-gamble/c360-cloud-run-migration`
- **Slack**: `#c360-platform-engineering`

---

**Version**: 2.0.0  
**Last Updated**: 2024-12-16  
**Maintainer**: C360 Platform Engineering Team

