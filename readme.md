# task E : Compliance and Supply Chain

The same flask webapp will be used for this task. 

## PS.1 - Protect software
Protecting software requires eliminating manual, undocumented interventions during the build phase. Relying on local built containers has the risk of human tampering. By using GitHub actions, the process is shifted to a clean room environment and ```my-flask-app:v1```  is constructed on a ```ubuntu-latest``` runner.  

- #### yml_file_config
![](./screenshots/yml_file_config.png)

---
```python:3.9-slim``` was chosen sine it has standard debian and has a image size of around 120MB, which is a sweet spot and functional at the same time. Furthermore, the Dockerfile intentionally avoids executing as the default root user. By running ```groupadd -r securitygroup && useradd -r -g securitygroup appuser```, the build creates a dedicated, low-privilege system user to harden against tampering and exploits.

- #### Dockerfile_config
![](./screenshots/Dockerfile_config.png)
---
Uses the latest flask version.
- #### requirement_file
![](./screenshots/requirement.png)
---

This is mainly for the local container built since most of these files are not uploaded to github anyways. 
- #### .dockerignore_config
![](./screenshots/dockerignore.png)

---
Workflow result after pushing. 
- #### Workflow_overview
![](./screenshots/Workflow_overview.png)
---
Successful built the container.

- #### Container_successfully_built
![](./screenshots/ss1.png)

---
**Combining an automated CI/CD pipeline with a securely scoped, non-root container architecture ensures that the build is tamper-evident and resistant to post-deployment modification, directly satisfying the objectives of PS.1.**
---

## PW.4 - Produce Well-Secured Software

```requirements.txt file``` is just a request for dependencies, it does not guarantee the actual compiled state of the software. To achieve true supply chain transparency, the pipeline utilizes Anchore Syft to inspect the compiled ```my-flask-app:v1``` container layers. The output is explicitly configured as a CycloneDX JSON file which is a standardized, machine-readable format purpose-built for software supply chain security.

- #### Generate_CycloneDX_SBOM
![](./screenshots/Generate_CycloneDX_SBOM.png)

- #### upload_artifact
![](./screenshots/upload_artifact.png)

---

Uploaded artifact can be downloaded here.
- #### SBOM_output
![](./screenshots/SBOM_output.png)
---

- #### SBOM_content
![](./SBOM_content.png)

---

## RV.1 - Respond to Vulnerabilities

Generating an SBOM as a static document is insufficient for rapid incident response. Because the previous Syft step is configured to output cyclonedx-json, the subsequent pipeline step can programmatically ingest this standard. The pipeline utilizes Aqua Trivy points it directly at the freshly generated artifact  ```scan-ref: 'sbom.json'``` and the result will be log as a table format for easier reading ```format: 'table'```.
The pipeline is configured so that it has a failure condition if it discovers vulnerability with a CRITICAL severity.  

- #### 
![](./Enforce_Vulnerability_Policy.png)

This architectural design ensures that vulnerability scanning is inherently coupled with the build process
---





