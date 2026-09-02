REGISTRY	?=lcr.loongnix.cn
ORGANIZATION	?=rancher
REPOSITORY	?=rancher
TAG		?=v2.6.13
LATEST		?=true

IMAGE=$(REGISTRY)/$(ORGANIZATION)/$(REPOSITORY):$(TAG)
IMAGE_AGENT=$(REGISTRY)/$(ORGANIZATION)/$(REPOSITORY)-agent:$(TAG)
LATEST_IMAGE=$(REGISTRY)/$(ORGANIZATION)/$(REPOSITORY):latest
LATEST_IMAGE_AGENT=$(REGISTRY)/$(ORGANIZATION)/$(REPOSITORY)-agent:latest
SOURCE_URL=https://github.com/rancher/rancher.git
SOURCE=$(shell echo $(SOURCE_URL) | awk -F '/' '{print $$NF}' | awk -F '.' '{print $$1}')
PATCH1=0001-debian.patch
PATCH2=0002-system-agent.patch

default: image

src/$(SOURCE):
	git clone -b $(TAG) --depth 1 $(SOURCE_URL) $@
	mkdir -p $@/package/bins
	curl -sLf https://raw.githubusercontent.com/rancher/system-agent/v0.2.13/install.sh -o $@/package/system-agent-install.sh
	curl -sLf https://releases.rancher.com/kontainer-driver-metadata/release-v2.6/data.json -o $@/package/data.json
	curl -sL https://cloud.loongnix.cn/releases/loongarch64/rancher/k3s-airgap-images/rancher-v2.6.13/k3s-airgap-images.tar.gz | tar -xvzf - -C $@/package/bins
	curl -sL https://cloud.loongnix.cn/releases/loongarch64/rancher/k3s-airgap-images/rancher-v2.6.13/extra-images.tar.gz | tar -xvzf - -C $@/package/bins
	curl -sL https://cloud.loongnix.cn/releases/loongarch64/rancher/rancher/v2.6.13/rancher-linux-loong64.tar.gz | tar -xvzf - -C $@/package/bins
	curl -sL https://cloud.loongnix.cn/releases/loongarch64/rancher/rancher/v2.6.13/agent-linux-loong64.tar.gz | tar -xvzf - -C $@/package/bins
	cd $@ && git apply $(CURDIR)/$(PATCH1) && git apply $(CURDIR)/$(PATCH2)

image: src/$(SOURCE)
	docker build \
		--build-arg http_proxy=$(http_proxy) \
		--build-arg https_proxy=$(https_proxy) \
		--build-arg VERSION=$(TAG) \
		--build-arg IMAGE_REPO=$(REGISTRY)/$(ORGANIZATION) \
		-f Dockerfile \
		-t $(IMAGE) \
		.
	docker build \
		--build-arg http_proxy=$(http_proxy) \
		--build-arg https_proxy=$(https_proxy) \
		-f Dockerfile.agent \
		-t $(IMAGE_AGENT) \
		.

push:
	docker push $(IMAGE)
	docker push $(IMAGE_AGENT)
	#latest image
	@if [ $(LATEST) = "true" ]; \
		then \
		docker tag $(IMAGE) $(LATEST_IMAGE); \
		docker tag $(IMAGE_AGENT) $(LATEST_IMAGE_AGENT); \
		docker push $(LATEST_IMAGE); \
		docker push $(LATEST_IMAGE_AGENT); \
	fi

clean:
	rm -rf src
