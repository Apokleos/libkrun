ARCH = $(shell uname -m)
OS = $(shell uname -s)
LDFLAGS_x86_64_Linux = -lkrun
LDFLAGS_aarch64_Linux = -lkrun
LDFLAGS_arm64_Darwin = -L/opt/homebrew/lib -lkrun
LDFLAGS_sev = -lkrun-sev
CFLAGS_Linux = -O2 -g
CFLAGS_Darwin = -O2 -g -I/opt/homebrew/include
ROOTFS_DISTRO := fedora
ROOTFS_DIR = rootfs_$(ROOTFS_DISTRO)

.PHONY: clean rootfs

ifeq ($(SEV),1)
    EXAMPLES := launch-tee
else
    EXAMPLES := chroot_vm
endif

all: $(EXAMPLES)

chroot_vm: chroot_vm.c
	gcc -o $@ $< $(CFLAGS_$(OS)) $(LDFLAGS_$(ARCH)_$(OS))
ifeq ($(OS),Darwin)
	codesign --entitlements chroot_vm.entitlements --force -s - $@
endif

launch-tee: launch-tee.c
	gcc -o $@ $< $(CFLAGS_$(OS)) $(LDFLAGS_sev)

# Build the rootfs to be used with chroot_vm.
rootfs:
	mkdir -p $(ROOTFS_DIR)
	podman create --name libkrun_chroot_vm $(ROOTFS_DISTRO)
	podman export libkrun_chroot_vm | tar xpf - -C $(ROOTFS_DIR)
	podman rm libkrun_chroot_vm

clean:
	rm -rf chroot_vm $(ROOTFS_DIR) launch-tee
