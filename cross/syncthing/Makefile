PKG_NAME = syncthing
PKG_VERS = 0.14.49
PKG_EXT = tar.gz
PKG_DIST_NAME = $(PKG_NAME)-source-v$(PKG_VERS).$(PKG_EXT)
PKG_DIST_SITE = https://github.com/syncthing/syncthing/releases/download/v$(PKG_VERS)
PKG_DIR = $(PKG_NAME)

DEPENDS = native/go

HOMEPAGE = http://www.syncthing.net/
COMMENT  = Syncthing replaces Dropbox and BitTorrent Sync with something open, trustworthy and decentralized. Your data is your data alone and you deserve to choose where it is stored, if it is shared with some third party and how ...
LICENSE  = MPL-2.0

CONFIGURE_TARGET = nope
COMPILE_TARGET = syncthing_compile
INSTALL_TARGET = syncthing_install

GOOS = linux
CGO_ENABLED = 0

# set this to 1 to create static linked binaries
STATIC_BINARIES = 0

include ../../mk/spksrc.cross-cc.mk

# Define SYNCTHING_ARCH as per Syncthing standards
ifeq ($(findstring $(ARCH),$(ARM5_ARCHES)),$(ARCH))
SYNCTHING_ARCH = arm
ENV += GOARM=5
endif
ifeq ($(findstring $(ARCH),$(ARM7_ARCHES)),$(ARCH))
SYNCTHING_ARCH = arm
ENV += GOARM=7
endif
ifeq ($(findstring $(ARCH),$(ARM8_ARCHES)),$(ARCH))
SYNCTHING_ARCH = arm64
endif
ifeq ($(findstring $(ARCH),$(x86_ARCHES)),$(ARCH))
SYNCTHING_ARCH = 386
endif
ifeq ($(findstring $(ARCH),$(x64_ARCHES)),$(ARCH))
SYNCTHING_ARCH = amd64
endif
# Go has no ppc32 support for qoriq https://github.com/golang/go/issues/22885
#ifeq ($(findstring $(ARCH),$(PPC_ARCHES)),$(ARCH))
#SYNCTHING_ARCH = ppc32
#endif
ifeq ($(SYNCTHING_ARCH),)
$(error Unsupported ARCH $(ARCH))
endif

EXTRACT_PATH = $(WORK_DIR)/src/github.com/$(PKG_NAME)
SYNCTHING_DIR = $(EXTRACT_PATH)/$(PKG_NAME)
SYNCTHING_BIN = $(SYNCTHING_DIR)/syncthing

# use workdir as gopath
ENV += GOPATH=$(WORK_DIR)
ENV += CGO_ENABLED=$(CGO_ENABLED)
ENV += PATH=$(WORK_DIR)/../../../native/go/work-native/go/bin/:$$PATH

CONF_ARGS = -goos=$(GOOS) -goarch=$(SYNCTHING_ARCH) -version=v$(PKG_VERS)

ifeq ($(strip $(STATIC_BINARIES)),1)
CONF_ARGS += -no-upgrade
endif

syncthing_compile:
	cd $(SYNCTHING_DIR) && env $(ENV) go run build.go $(CONF_ARGS) build

syncthing_install:
	mkdir -p $(STAGING_INSTALL_PREFIX)/bin
	install -m 755 $(SYNCTHING_BIN) $(STAGING_INSTALL_PREFIX)/bin/
