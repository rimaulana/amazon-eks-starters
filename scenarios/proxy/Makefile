BUCKET ?= gresik
REGION ?= us-east-1

.PHONY: validate
validate: generate
	ls | grep -v -e aws-auth | grep -v -e proxy-cm | grep .yaml | xargs cfn-lint

.PHONY: package
package:
	aws cloudformation package --template ./main.yaml --s3-bucket $(BUCKET) --output-template cfn.yaml --region $(REGION);

.PHONY: generate
generate: clean
	eks-ami-updater \
		--template worker.tmpl \
		--output worker.yaml;

.PHONY: clean
clean:
	rm -f worker.yaml;
	rm -f cfn.yaml;
	rm -f eks-ami-updater.log