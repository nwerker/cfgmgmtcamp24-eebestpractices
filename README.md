# Execution Environment Best Practices

These best practices are part of the CfgMgmtCamp 2024 Talk "Ansible Execution Environment Best Practices & Automation".

> [!IMPORTANT]  
> These best practices where curated with the best interest in mind. Please feel welcome to contribute / fork / discuss any of these topics or reach out if there are questions.

## Use Collection level Metadata

* Dependencies will be automatically installed, since defined on Collection level
* No dependency management and resolving conflicts by hand
* Listing Collections in requirements.yml should be enough
* Done via following files on Collection Level
	* meta/execution-environment.yml (if naming best practices are not followed)
	* requirements.txt
	* bindep.txt
* Community participation: Please open GitHub issues when Collections used by you have noCollection level Metadata yet

## Base images

* Don‘t use default ansible-runner image
	* deprecated / ansible-core 2.12 & CentOS 8 stream image
* Use Image according to your needs
	* awx-ee can be used as base image but shouldn‘t necessarily
	* Only: if there is a big overlap in the included collections and dependencies
	* Image size: 1.74 GB
		* fedora:39 in comparison: 264 MB
* To enterprise customers: use what you pay for (especially when it comes to support cases)
	* ansible-automation-platform-21 (ee-minimal / ee-supported etc.)
	* ansible-automation-platform-22
	* ansible-automation-platform-23
	* ansible-automation-platform-XX

## Naming 

* Use default naming
	* execution-enviroment.yml
	* requirements.yml
	* requirements.txt
	* bindep.txt
	* ansible.cfg

## Execution Environment Definition

The execution environment definition is updated regularly with new ansible-builder versions, offering a lot of new & useful features: 

* v1:
	* All ansible-builder versions
* v2:
	* ansible-builder version 1.2 upwards
	* Adds podman container image signing
* v3:
	* Ansible-builder version 3.0 upwards
	* Allows for granular choice of ansible-core, ansible-runner & python version
	* Deprecates ansible-runner Base image
	* Adds build options
	* Fine grained additional build steps (8 instead of previously 2)

## Tooling

* ansible-builder as the preferred tool for building execution environment contexts and / or images
* ansible-navigator as the preferred tool to inspect images and run automation via cli in execution environments
* ansible-runner as library for M2M communication
	* Don't use the CLI capabilites, ansible-runner is not built for that
* OCI
	* No feature parity between podman and docker out of the box
	* podman and buildah as preferred tools

## MISC

* Scope
	* Highly dependent on use case, automated domain and user group
	* Maintain as little as possible as much as necessary
* Align release management und testing with your ansible workflow
* Use Collection overloading with care, maybe dependencies will break
	* Instead build a new Execution Environment
* No unnecessary mounting of paths to consume binaries
* No unnecessary delegation of tasks to evade EE
* Support:
	* Use of correct base images
	* Use of correct tool versions


# Ansible Best Practices

* Latest greatest collection versions, where appropriate:
	* Especially in public cloud collections a lot of breaking changes
	* New features and robustness
	* Avoid technical debt building up

# Container Best Practices

* Frequently (preferably automated) builds with updated base image
* Slim / storage friendly images
	* Avoid including unused assets into the image
	* Think in Layers and Blobs when it comes to “Additional Build Steps“
* Lifecycle: Regularly cleanups
	* Build environment
	* Dangling images on hosts
	* Unused images in registry
* Tagging
	* Semver x.v.z
	* Calver YYYYMMDD
* Consider Multi-arch builds for use Execution Environments on different platforms


# Security Best Practices

* Container Best Practice: Rebuild images scheduled
	* Because of bug and security fixes in base image and dependencies
* Security Scans at build and at rest 
	* CVEs
	* Secrets
	* Robustness
	* Tools: Trivy
	* Bult into container registries: Harbor, quay.io, GitLab
* Secure your container registry
* Seperate build and production environment
* Reduce container privileges during runtime (podman rootles ftw)
* Sign images (e.g. with podman and ansible-builder >= 1.2.0)

# Automation 

No „defacto“ standard. Use what fit's your usecase

* Ansible:
  * [redhat_cop.ee_utilities Collection](https://galaxy.ansible.com/ui/repo/published/redhat_cop/ee_utilities/)

* CI/CD:
  * [Automating execution environment image builds with GitHub Actions](https://www.ansible.com/blog/automating-execution-environment-image-builds-with-github-actions)
  * [How to Build Ansible Execution Environments with OpenShift Pipelines](https://www.redhat.com/en/blog/how-to-build-ansible-execution-environments-with-openshift-pipelines)
    * [Tekton Task](https://hub.tekton.dev/tekton/task/ansible-builder)
  * DIY:
    * Best practice: ansible-builder creates build context, buildah builds the image
    * Think about the user: Automated releases with change logs
