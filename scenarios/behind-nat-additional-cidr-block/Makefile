BUCKET ?= gresik
BUCKETREGION ?= us-east-1
STACKREGION ?= us-east-1
STACKNAME ?= k8s-dev

.PHONY: package
package: validate
	aws cloudformation package \
		--template ./main.yaml \
		--s3-bucket $(BUCKET) \
		--output-template cfn.yaml \
		--region $(BUCKETREGION);

.PHONY: validate
validate: generate
	ls | grep -v -e aws-auth | grep .yaml | xargs cfn-lint

.PHONY: depcheck
depcheck:
	$(shell grep -v '^\s*\(#\|$$\)' parameters.conf)

.PHONY: generate
generate: clean
	eks-ami-updater \
		--template worker.tmpl \
		--output worker.yaml;

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