BUCKET ?= default
REGION ?= us-east-1

.PHONY: package
package:
	aws cloudformation package --template ./main.yaml --s3-bucket $(BUCKET) --output-template cfn.yaml --region $(REGION);