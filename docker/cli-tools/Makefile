#!/usr/bin/env make
REGISTRY_URL?=gcr.io/engineering-devops
# build master as latest
VERSION?=latest
_VERSION=$(subst master,latest,$(VERSION))
SHELL:=/bin/bash
DOCKER_BUILD:=docker build --build-arg VERSION=$(_VERSION) --build-arg FORGEOPS_REGISTRY=$(REGISTRY_URL)


foo:
	echo $(DOCKER_BUILD)

repo-build:
	$(DOCKER_BUILD) -t $(REGISTRY_URL)/repo:$(_VERSION) -f docker/cli-tools/repo/Dockerfile .

repo: repo-build push/repo/$(_VERSION)
	@echo "completed building repo tools"

cdm-build:
	$(DOCKER_BUILD) -t $(REGISTRY_URL)/cdm-cli:$(_VERSION) -f docker/cli-tools/cdm/Dockerfile .

cdm: yamkt cdm-build push/cdm-cli/$(_VERSION)
	@echo "completed building cdk"

cdk-build:
	$(DOCKER_BUILD) --cache-from $(REGISTRY_URL)/yamkt:$(_VERSION)-skaffold -t $(REGISTRY_URL)/cdk-cli:$(_VERSION) -f docker/cli-tools/cdk/Dockerfile .

cdk: yamkt cdk-build push/cdk-cli/$(_VERSION)
	@echo "completed building cdk"

toolbox-build:
	$(DOCKER_BUILD) --cache-from $(REGISTRY_URL)/yamkt:$(_VERSION)-skaffold -t $(REGISTRY_URL)/cdk-toolbox:$(_VERSION) docker/cli-tools/toolbox

toolbox: yamkt toolbox-build push/cdk-toolbox/$(_VERSION)
	@echo "completed building toolbox"

debug-build:
	$(DOCKER_BUILD) -t $(REGISTRY_URL)/debug-cli:$(_VERSION) -f docker/cli-tools/debug/Dockerfile .

debug: yamkt debug-build push/debug-cli/$(_VERSION)
	@echo "completed building cdk"

cli-tools: cdm cdk toolbox debug
	@echo "completed building cli tools"

yamkt-build/%:
	$(DOCKER_BUILD) -t $(REGISTRY_URL)/yamkt:$(_VERSION)-$(@F) --target $(@F) -f docker/cli-tools/base/Dockerfile .

yamkt-build:
	$(DOCKER_BUILD) -t $(REGISTRY_URL)/yamkt:$(_VERSION) -f docker/cli-tools/base/Dockerfile .

yamkt: yamkt-build push/yamkt/$(_VERSION) yamkt-build/skaffold push/yamkt/$(_VERSION)-skaffold
	@echo "completed build and push of Yet Another Multicloud Kubernetes Toolkit: $(_VERSION) to $(REGISRY_URL)"

push/%:
	docker push $(REGISTRY_URL)/$(*D):$(@F)

all: cli-tools
