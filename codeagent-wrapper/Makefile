GO ?= go
VERSION := $(shell git describe --tags --always --dirty 2>/dev/null || echo dev)
LDFLAGS := -ldflags "-X codeagent-wrapper/internal/app.version=$(VERSION)"
CROSS_LDFLAGS := -ldflags "-s -w -X codeagent-wrapper/internal/app.version=$(VERSION)"

DIST_DIR := dist

TOOLS_BIN := $(CURDIR)/bin
TOOLCHAIN ?= go1.22.0
GOLANGCI_LINT_VERSION := v1.56.2
STATICCHECK_VERSION := v0.4.7

GOLANGCI_LINT := $(TOOLS_BIN)/golangci-lint
STATICCHECK := $(TOOLS_BIN)/staticcheck

.PHONY: build build-all \
        build-linux-amd64 build-linux-arm64 \
        build-darwin-amd64 build-darwin-arm64 \
        build-windows-amd64 build-windows-arm64 \
        test lint clean install

build:
	$(GO) build $(LDFLAGS) -o codeagent-wrapper ./cmd/codeagent-wrapper

# Cross-compile for all platforms; output goes to dist/
build-all: build-linux-amd64 build-linux-arm64 \
           build-darwin-amd64 build-darwin-arm64 \
           build-windows-amd64 build-windows-arm64
	@echo "✅ All binaries written to $(DIST_DIR)/"
	@ls -lh $(DIST_DIR)/

build-linux-amd64:
	@mkdir -p $(DIST_DIR)
	CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
		$(GO) build $(CROSS_LDFLAGS) -o $(DIST_DIR)/codeagent-wrapper-linux-amd64 ./cmd/codeagent-wrapper

build-linux-arm64:
	@mkdir -p $(DIST_DIR)
	CGO_ENABLED=0 GOOS=linux GOARCH=arm64 \
		$(GO) build $(CROSS_LDFLAGS) -o $(DIST_DIR)/codeagent-wrapper-linux-arm64 ./cmd/codeagent-wrapper

build-darwin-amd64:
	@mkdir -p $(DIST_DIR)
	CGO_ENABLED=0 GOOS=darwin GOARCH=amd64 \
		$(GO) build $(CROSS_LDFLAGS) -o $(DIST_DIR)/codeagent-wrapper-darwin-amd64 ./cmd/codeagent-wrapper

build-darwin-arm64:
	@mkdir -p $(DIST_DIR)
	CGO_ENABLED=0 GOOS=darwin GOARCH=arm64 \
		$(GO) build $(CROSS_LDFLAGS) -o $(DIST_DIR)/codeagent-wrapper-darwin-arm64 ./cmd/codeagent-wrapper

build-windows-amd64:
	@mkdir -p $(DIST_DIR)
	CGO_ENABLED=0 GOOS=windows GOARCH=amd64 \
		$(GO) build $(CROSS_LDFLAGS) -o $(DIST_DIR)/codeagent-wrapper-windows-amd64.exe ./cmd/codeagent-wrapper

build-windows-arm64:
	@mkdir -p $(DIST_DIR)
	CGO_ENABLED=0 GOOS=windows GOARCH=arm64 \
		$(GO) build $(CROSS_LDFLAGS) -o $(DIST_DIR)/codeagent-wrapper-windows-arm64.exe ./cmd/codeagent-wrapper

test:
	$(GO) test ./...

$(GOLANGCI_LINT):
	@mkdir -p $(TOOLS_BIN)
	GOTOOLCHAIN=$(TOOLCHAIN) GOBIN=$(TOOLS_BIN) $(GO) install github.com/golangci/golangci-lint/cmd/golangci-lint@$(GOLANGCI_LINT_VERSION)

$(STATICCHECK):
	@mkdir -p $(TOOLS_BIN)
	GOTOOLCHAIN=$(TOOLCHAIN) GOBIN=$(TOOLS_BIN) $(GO) install honnef.co/go/tools/cmd/staticcheck@$(STATICCHECK_VERSION)

lint: $(GOLANGCI_LINT) $(STATICCHECK)
	GOTOOLCHAIN=$(TOOLCHAIN) $(GOLANGCI_LINT) run ./...
	GOTOOLCHAIN=$(TOOLCHAIN) $(STATICCHECK) ./...

clean:
	@python3 -c 'import glob, os; paths=["codeagent","codeagent.exe","codeagent-wrapper","codeagent-wrapper.exe","coverage.out","cover.out","coverage.html"]; paths += glob.glob("coverage*.out") + glob.glob("cover_*.out") + glob.glob("*.test"); [os.remove(p) for p in paths if os.path.exists(p)]'
	@rm -rf $(DIST_DIR)

install:
	$(GO) install $(LDFLAGS) ./cmd/codeagent-wrapper
