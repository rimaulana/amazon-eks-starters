BUCKET ?= gresik
SHELL = /bin/bash
BUCKETREGION ?= us-east-1
STACKREGION ?= us-east-1
STACKNAME ?= k8s-dev
K8S_VERSION = $(shell grep K8sVersion parameters.conf | cut -d"=" -f2)
GPU_SUPPORT = $(shell grep -v '^\s*\(\#\|$$\)' parameters.conf | grep NodeGPUSupport | cut -d"=" -f2)

.PHONY: package
package: validate
	aws cloudformation package \
		--template ./main.yaml \
		--s3-bucket $(BUCKET) \
		--output-template cfn.yaml \
		--region $(BUCKETREGION);

.PHONY: validate
validate: generate 
	ls | grep -v -e aws-auth | grep -v -e custom-networking | grep .yaml | xargs cfn-lint

.PHONY: depcheck
depcheck:
	$(shell grep -v '^\s*\(#\|$$\)' parameters.conf)

.PHONY: generate
generate: clean
	cp worker.tmpl worker.yaml;
	sed -i s,K8S_VERSION,1.$(K8S_VERSION),g worker.yaml;
	if [[ "$(GPU_SUPPORT)" == "false" || -z "$(GPU_SUPPORT)" ]]; then \
    	sed -i s,IMAGE_TYPE,amazon-linux-2,g worker.yaml; \
    else \
		sed -i s,IMAGE_TYPE,amazon-linux-2-gpu,g worker.yaml; \
	fi

.PHONY: deploy
deploy: package
	aws cloudformation deploy \
		--template-file cfn.yaml \
		--stack-name $(STACKNAME) \
		--region $(STACKREGION) \
		--capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM \
		--parameter-overrides $(shell grep -v '^\s*\(#\|$$\)' parameters.conf)

.PHONY: clean
clean:
	rm -f worker.yaml;
	rm -f cfn.yaml;
	rm -f eks-ami-updater.log
