AZKS := epiphanyplatform/azks:0.0.2

#makes it easier to replace the value
M_NAME            ?= azks-example-tests
M_SHARED_EXTERNAL ?= /tmp/$(M_NAME)/shared

VMS_RSA_FILENAME ?= vms_rsa

-include ./azure.mk

export

.PHONY: all apply destroy clean metadata setup

all: apply

apply: init-azks plan-azks apply-azks

destroy: destroy-plan-azks destroy-azks

clean:
	@rm -rf $(M_SHARED_EXTERNAL)

metadata: guard-VERSION guard-IMAGE guard-USER
	@docker run --rm \
		-t $(USER)/$(IMAGE):$(VERSION) \
		metadata

.PHONY: init-azks plan-azks apply-azks output-azks destroy-plan-azks destroy-azks

init-azks: setup
	@docker run --rm \
		-v $(M_SHARED_EXTERNAL):/shared \
		-t $(AZKS) \
		init \
		M_VMS_COUNT=2 \
		M_PUBLIC_IPS=false \
		M_NAME=$(M_NAME) \
		M_VMS_RSA=$(VMS_RSA_FILENAME) \
		M_RG_NAME=$(M_RG_NAME) \
		M_VNET_NAME=$(M_VNET_NAME) \
		M_SUBNET_NAME=$(M_SUBNET_NAME)

plan-azks: setup
	@docker run --rm \
		-v $(M_SHARED_EXTERNAL):/shared \
		-t $(AZKS) \
		plan \
		M_ARM_CLIENT_ID=$$ARM_CLIENT_ID \
		M_ARM_CLIENT_SECRET=$$ARM_CLIENT_SECRET \
		M_ARM_SUBSCRIPTION_ID=$$ARM_SUBSCRIPTION_ID \
		M_ARM_TENANT_ID=$$ARM_TENANT_ID

apply-azks: setup
	@docker run --rm \
		-v $(M_SHARED_EXTERNAL):/shared \
		-t $(AZKS) \
		apply \
		M_ARM_CLIENT_ID=$$ARM_CLIENT_ID \
		M_ARM_CLIENT_SECRET=$$ARM_CLIENT_SECRET \
		M_ARM_SUBSCRIPTION_ID=$$ARM_SUBSCRIPTION_ID \
		M_ARM_TENANT_ID=$$ARM_TENANT_ID \
		kubeconfig

output-azks: setup
	@docker run --rm \
		-v $(M_SHARED_EXTERNAL):/shared \
		-t $(AZKS) \
		output \
		M_ARM_CLIENT_ID=$$ARM_CLIENT_ID \
		M_ARM_CLIENT_SECRET=$$ARM_CLIENT_SECRET \
		M_ARM_SUBSCRIPTION_ID=$$ARM_SUBSCRIPTION_ID \
		M_ARM_TENANT_ID=$$ARM_TENANT_ID

destroy-plan-azks: setup
	@docker run --rm \
		-v $(M_SHARED_EXTERNAL):/shared \
		-t $(AZKS) \
		plan-destroy \
		M_ARM_CLIENT_ID=$$ARM_CLIENT_ID \
		M_ARM_CLIENT_SECRET=$$ARM_CLIENT_SECRET \
		M_ARM_SUBSCRIPTION_ID=$$ARM_SUBSCRIPTION_ID \
		M_ARM_TENANT_ID=$$ARM_TENANT_ID

destroy-azks: setup
	@docker run --rm \
		-v $(M_SHARED_EXTERNAL):/shared \
		-t $(AZKS) \
		destroy \
		M_ARM_CLIENT_ID=$$ARM_CLIENT_ID \
		M_ARM_CLIENT_SECRET=$$ARM_CLIENT_SECRET \
		M_ARM_SUBSCRIPTION_ID=$$ARM_SUBSCRIPTION_ID \
		M_ARM_TENANT_ID=$$ARM_TENANT_ID

setup: $(M_SHARED_EXTERNAL)/$(VMS_RSA_FILENAME)

$(M_SHARED_EXTERNAL)/$(VMS_RSA_FILENAME):
	@mkdir -p $(dir $@)
	@ssh-keygen -t rsa -b 4096 -f $@ -N '' 2>&1 >/dev/null
