APP_NAME := csi-sanity
VER :=$(shell git describe)
BRANCH := $(subst /,-,$(shell git rev-parse --abbrev-ref HEAD))
SHA := $(shell git rev-parse --short HEAD)
ARCH := $(shell go env GOARCH)
GOOS := $(shell go env GOOS)
DIR=.

ifdef APP_SUFFIX
  VERSION = $(VER)-$(subst /,-,$(APP_SUFFIX))
else
ifeq (master,$(BRANCH))
  VERSION = $(VER)
else
  VERSION = $(VER)-$(BRANCH)
endif
endif

LDFLAGS :=-ldflags "-w -X github.com/kubernetes-csi/csi-test/cmd/csi-sanity.VERSION=$(VERSION) -extldflags '-z relro -z now'"
PACKAGE :=$(DIR)/dist/$(APP_NAME)-$(VERSION).$(GOOS).$(ARCH).tar.gz

all: $(APP_NAME)

$(APP_NAME): Makefile sanity_test.go
	go test $(LDFLAGS) -c -o $(APP_NAME)

clean:
	rm -f csi-sanity
	rm -rf $(DIR)/dist

dist: $(PACKAGE)

$(PACKAGE): $(APP_NAME)
	@echo Packaging Binaries...
	@mkdir -p tmp/$(APP_NAME)
	@cp $(APP_NAME) tmp/$(APP_NAME)/
	@mkdir -p $(DIR)/dist/
	tar -czf $@ -C tmp $(APP_NAME);
	@rm -rf tmp
	@echo
	@echo Package $@ saved in dist directory

linux_amd64_dist:
	GOOS=linux GOARCH=amd64 $(MAKE) dist

linux_arm_dist:
	GOOS=linux GOARCH=arm $(MAKE) dist

linux_arm64_dist:
	GOOS=linux GOARCH=arm64 $(MAKE) dist

darwin_amd64_dist:
	GOOS=darwin GOARCH=amd64 $(MAKE) dist

release: darwin_amd64_dist linux_arm_dist linux_amd64_dist linux_arm64_dist

.PHONY: release darwin_amd64_dist linux_arm64_dist linux_amd64_dist \
	    linux_arm_dist linux_amd64_dist clean
