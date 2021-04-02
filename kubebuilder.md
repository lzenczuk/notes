# Kubebuilder - kubernetes operators development

Kubebuilder have preatty good [documentation](https://book.kubebuilder.io/) which those commands based on.

## Setup
Just go through [quick start](https://kubebuilder.io/quick-start.html).

## Development
# Create new project
This will create go project and initialize kubebuilder in it
```bash
mkdir mykubeop
cd mykubeop
go mod init mykubeop
kubebuilder init --domain lzenczuk.github.com
```
This will create bunch of files and folders. One worth to modify is `hack/boilerplate.go.txt` which contains header (licence info) added to all generated go files.

# Add API - define new kind and related controller
```bash
kubebuilder create api --group batch --version v1 --kind CronJob
```
