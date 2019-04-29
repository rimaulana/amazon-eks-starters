BUCKET ?= gresik
BUCKETREGION ?= us-east-1
STACKREGION ?= us-east-1
STACKNAME ?= k8s-dev

.PHONY: package
package: generate
	aws cloudformation package --template ./main.yaml --s3-bucket $(BUCKET) --output-template cfn.yaml --region $(BUCKETREGION);

.PHONY: validate
validate:
	ls | grep .yaml | xargs cfn-lint

.PHONY: depcheck
depcheck:

.PHONY: generate
generate:
	eks-ami-updater --template worker.tmpl --output worker.yaml

.PHONY: deploy
deploy: package
	aws cloudformation deploy --template-file cfn.yaml --stack-name $(STACKNAME) --region $(STACKREGION) --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM --parameter-overrides $(shell cat parameters.conf)