include ../app.Makefile
include ../crd.Makefile
include ../gcloud.Makefile
include ../var.Makefile

TAG ?= 0.8.0
TAG_RELEASE ?= $(TAG)
NAME ?= agones


$(info ---- TAG = $(TAG))
$(info ---- TAG_RELEASE = $(TAG_RELEASE))


# source repo and destination tags for images
SOURCE_REGISTRY ?= gcr.io/agones-images
APP_DEPLOYER_IMAGE ?= $(REGISTRY)/agones/deployer:$(TAG)
AGONES_CONTROLLER_IMAGE ?= $(REGISTRY)/agones/agones-controller:$(TAG)
AGONES_SDK_IMAGE ?= $(REGISTRY)/agones/agones-sdk:$(TAG)
AGONES_PING_IMAGE ?= $(REGISTRY)/agones/agones-ping:$(TAG)


XONOTIC_DEMO_BASE=https://github.com/GoogleCloudPlatform/agones/raw/release-$(TAG_RELEASE)/examples/xonotic


APP_PARAMETERS ?= { \
    "APP_INSTANCE_NAME": "$(NAME)", \
    "NAMESPACE": "$(NAMESPACE)" \
}


APP_TEST_PARAMETERS ?= { \
    "INSTALL_XONOTIC_DEMO": "true" \
}


app/build:: .build/agones/helmdeps \
            .build/agones/xonotic.yaml \
            .build/agones/deployer \
            .build/agones/agones-controller \
            .build/agones/agones-sdk \
            .build/agones/agones-ping


.build/agones: | .build
	mkdir -p "$@"


.build/agones/helmdeps: chart/agones-mp/requirements.* \
                        | .build/agones
	(helm repo list | grep "https://agones.dev/chart/stable") || \
	    helm repo add agones "https://agones.dev/chart/stable"
	helm dependency build chart/agones-mp
	@touch "$@"


.build/agones/xonotic.yaml: | .build
	wget -q -O $@.tmp $(XONOTIC_DEMO_BASE)/fleet.yaml
	if [[ -s $@.tmp ]] ; then mv $@.tmp $@ ; fi


.build/agones/deployer: deployer/* \
                        chart/agones-mp/* \
                        chart/agones-mp/charts/* \
                        chart/agones-mp/templates/* \
                        scripts/* \
                        schema.yaml \
                        .build/var/APP_DEPLOYER_IMAGE \
                        .build/var/MARKETPLACE_TOOLS_TAG \
                        .build/var/REGISTRY \
                        .build/var/TAG \
                        .build/agones/xonotic.yaml \
                        | .build/agones
	docker build \
	    --build-arg REGISTRY="$(REGISTRY)/agones" \
	    --build-arg TAG="$(TAG)" \
	    --build-arg MARKETPLACE_TOOLS_TAG="$(MARKETPLACE_TOOLS_TAG)" \
	    --build-arg XONOTIC_YAML=".build/agones/xonotic.yaml" \
	    --tag "$(APP_DEPLOYER_IMAGE)" \
	    -f deployer/Dockerfile \
	    .
	docker push "$(APP_DEPLOYER_IMAGE)"
	@touch "$@"


.build/agones/agones-controller: .build/var/REGISTRY \
                                 .build/var/TAG \
                                 | .build/agones
	docker pull $(SOURCE_REGISTRY)/agones-controller:$(TAG)
	docker tag $(SOURCE_REGISTRY)/agones-controller:$(TAG) \
	    "$(AGONES_CONTROLLER_IMAGE)"
	docker push "$(AGONES_CONTROLLER_IMAGE)"
	@touch "$@"


.build/agones/agones-sdk: .build/var/REGISTRY \
                          .build/var/TAG \
                          | .build/agones
	docker pull $(SOURCE_REGISTRY)/agones-sdk:$(TAG)
	docker tag $(SOURCE_REGISTRY)/agones-sdk:$(TAG) \
	    "$(AGONES_SDK_IMAGE)"
	docker push "$(AGONES_SDK_IMAGE)"
	@touch "$@"


.build/agones/agones-ping: .build/var/REGISTRY \
                           .build/var/TAG \
                           | .build/agones
	docker pull $(SOURCE_REGISTRY)/agones-ping:$(TAG)
	docker tag $(SOURCE_REGISTRY)/agones-ping:$(TAG) \
	    "$(AGONES_PING_IMAGE)"
	docker push "$(AGONES_PING_IMAGE)"
	@touch "$@"

