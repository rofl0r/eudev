ACLOCAL_AMFLAGS = -I m4 ${ACLOCAL_FLAGS}

AM_CPPFLAGS = \
	-include $(top_builddir)/config.h \
	-DMODPROBE=\"$(MODPROBE)\" \
	-DROOTPREFIX=\"$(rootprefix)\" \
	-DUDEV_CONF_DIR=\"$(udevconfdir)\" \
	-DUDEV_CONF_FILE=\"$(udevconffile)\" \
	-DUDEV_HWDB_BIN=\"$(udevhwdbbin)\" \
	-DUDEV_HWDB_DIR=\"$(udevhwdbdir)\" \
	-DUDEV_RULES_DIR=\"$(udevrulesdir)\" \
	-DUDEV_LIBEXEC_DIR=\"$(udevlibexecdir)\" \
	-DUDEV_VERSION=\"$(UDEV_VERSION)\" \
	-I $(top_srcdir)/src/libudev \
	-I $(top_srcdir)/src/udev

#
# For libudev
#

LIBUDEV_CURRENT=3
LIBUDEV_REVISION=0
LIBUDEV_AGE=2

define move-to-rootlibdir
        if test "$(libdir)" != "$(rootlibdir)"; then \
                $(MKDIR_P) $(DESTDIR)$(rootlibdir) && \
                so_img_name=$$(readlink $(DESTDIR)$(libdir)/$$libname) && \
                so_img_rel_target_prefix=$$(echo $(libdir) | sed 's,\(^/\|\)[^/][^/]*,..,g') && \
                ln -sf $$so_img_rel_target_prefix$(rootlibdir)/$$so_img_name $(DESTDIR)$(libdir)/$$libname && \
                mv $(DESTDIR)$(libdir)/$$libname.* $(DESTDIR)$(rootlibdir); \
        fi
endef

lib_LTLIBRARIES = \
	libudev.la

libudev_la_SOURCES =\
	libudev/libudev.c \
	libudev/libudev-list.c \
	libudev/libudev-util.c \
	libudev/libudev-device.c \
	libudev/libudev-enumerate.c \
	libudev/libudev-monitor.c \
	libudev/libudev-queue.c \
	libudev/libudev-hwdb.c \
	libudev/cgroup-util.c \
	libudev/conf-files.c \
	libudev/exit-status.c \
	libudev/hashmap.c \
	libudev/log.c \
	libudev/path-util.c \
	libudev/set.c \
	libudev/strbuf.c \
	libudev/strv.c \
	libudev/util.c

noinst_HEADERS = \
	libudev/libudev-hwdb-def.h \
	libudev/libudev-private.h \
	libudev/cgroup-util.h \
	libudev/conf-files.h \
	libudev/def.h \
	libudev/exit-status.h \
	libudev/hashmap.h \
	libudev/ioprio.h \
	libudev/log.h \
	libudev/macro.h \
	libudev/missing.h \
	libudev/path-util.h \
	libudev/set.h \
	libudev/socket-util.h \
	libudev/sparse-endian.h \
	libudev/strbuf.h \
	libudev/strv.h \
	libudev/util.h

include_HEADERS = \
	libudev/libudev.h

libudev_la_LDFLAGS = \
	$(AM_LDFLAGS) \
	-version-info $(LIBUDEV_CURRENT):$(LIBUDEV_REVISION):$(LIBUDEV_AGE) \
	-Wl,--version-script=$(top_srcdir)/src/libudev/libudev.sym

noinst_LTLIBRARIES = \
	libudev-private.la

libudev_private_la_SOURCES =\
	libudev/libudev-device-private.c \
	libudev/libudev-queue-private.c

libudev_private_la_LIBADD =\
	libudev.la

pkgconfiglib_DATA = \
	libudev/libudev.pc

EXTRA_DIST = \
	libudev/libudev.pc.in \
	libudev/libudev.sym

CLEANFILES = \
	libudev/libudev.pc

install-exec-hook:
	libname=libudev/libudev.so && $(move-to-rootlibdir)

uninstall-hook:
	rm -f $(DESTDIR)$(rootlibdir)/libudev.so*


#
# For udev
# 

sbin_PROGRAMS = \
	udev/udevd \
	udev/udevadm

udev_udevd_SOURCES = \
	udev/udevd.c

udev_udevd_LDADD = \
	libudev-core.la

udev_udevadm_SOURCES = \
	udev/udevadm.c \
	udev/udevadm-info.c \
	udev/udevadm-control.c \
	udev/udevadm-monitor.c \
	udev/udevadm-hwdb.c \
	udev/udevadm-settle.c \
	udev/udevadm-trigger.c \
	udev/udevadm-test.c \
	udev/udevadm-test-builtin.c

udev_udevadm_LDADD = \
	libudev-core.la

noinst_LTLIBRARIES += \
        libudev-core.la

libudev_core_la_SOURCES = \
	udev/udev-event.c \
	udev/udev-watch.c \
	udev/udev-node.c \
	udev/udev-rules.c \
	udev/udev-ctrl.c \
	udev/udev-builtin.c \
	udev/udev-builtin-btrfs.c \
	udev/udev-builtin-firmware.c \
	udev/udev-builtin-hwdb.c \
	udev/udev-builtin-input_id.c \
	udev/udev-builtin-net_id.c \
	udev/udev-builtin-path_id.c \
	udev/udev-builtin-usb_id.c \
	udev/dev-setup.c \
	udev/label.c \
	udev/mkdir.c

noinst_HEADERS += \
	udev/dev-setup.h \
	udev/label.h \
	udev/mkdir.h

include_HEADERS += \
	udev/udev.h

libudev_core_la_CFLAGS = \
	$(AM_CFLAGS) \
	$(BLKID_CFLAGS)

libudev_core_la_LIBADD = \
	libudev-private.la \
	$(BLKID_LIBS) \
	$(SELINUX_LIBS)

if HAVE_BLKID
libudev_core_la_SOURCES += \
	udev/udev-builtin-blkid.c
endif

if HAVE_MODULES
libudev_core_la_SOURCES += \
	udev/udev-builtin-kmod.c

if HAVE_LIBKMOD
libudev_core_la_CFLAGS += $(KMOD_CFLAGS)
libudev_core_la_LIBADD += $(KMOD_LIBS)
endif
endif

libudev_core_la_CPPFLAGS = \
	$(AM_CPPFLAGS) \
	-DFIRMWARE_PATH="$(FIRMWARE_PATH)"

#
# Extras
#
dist_udevconf_DATA = \
	udev/udev.conf

sharepkgconfig_DATA = \
	udev/udev.pc

EXTRA_DIST += \
	udev/udev.pc.in

CLEANFILES += \
	udev/udev.pc

udevlibexec_PROGRAMS = \
	accelerometer/accelerometer

accelerometer_accelerometer_SOURCES = \
	accelerometer/accelerometer.c

accelerometer_accelerometer_LDADD = \
	libudev-private.la \
	libudev-core.la \
	-lm

udevlibexec_PROGRAMS += \
	ata_id/ata_id

ata_id_ata_id_SOURCES = \
	ata_id/ata_id.c

ata_id_ata_id_LDADD = \
	libudev-private.la \
	libudev-core.la

udevlibexec_PROGRAMS += \
	cdrom_id/cdrom_id

cdrom_id_cdrom_id_SOURCES = \
	cdrom_id/cdrom_id.c

cdrom_id_cdrom_id_LDADD = \
	libudev-private.la \
	libudev-core.la

udevlibexec_PROGRAMS += \
	collect/collect

collect_collect_SOURCES = \
	collect/collect.c

collect_collect_LDADD = \
	libudev-private.la \
	libudev-core.la

udevlibexec_PROGRAMS += \
	scsi_id/scsi_id

scsi_id_scsi_id_SOURCES =\
	scsi_id/scsi_id.c \
	scsi_id/scsi_serial.c \
	scsi_id/scsi.h \
	scsi_id/scsi_id.h

scsi_id_scsi_id_LDADD = \
	libudev-private.la \
	libudev-core.la

EXTRA_DIST += \
	scsi_id/README

udevlibexec_PROGRAMS += \
	v4l_id/v4l_id

v4l_id_v4l_id_SOURCES = \
	v4l_id/v4l_id.c

v4l_id_v4l_id_LDADD = \
	libudev-private.la \
	libudev-core.la

udevlibexec_PROGRAMS += \
	mtd_probe/mtd_probe

mtd_probe_mtd_probe_SOURCES =  \
	mtd_probe/mtd_probe.c \
	mtd_probe/mtd_probe.h \
	mtd_probe/probe_smartmedia.c

#
# GUDEV
#

if ENABLE_GUDEV
LIBGUDEV_CURRENT=1
LIBGUDEV_REVISION=2
LIBGUDEV_AGE=1

libgudev_include_HEADERS = \
	gudev/gudev.h \
	gudev/gudevenums.h \
	gudev/gudevenumtypes.h \
	gudev/gudevtypes.h \
	gudev/gudevclient.h \
	gudev/gudevdevice.h \
	gudev/gudevenumerator.h

EXTRA_DIST += \
	gudev/gudev-1.0.pc.in \
	gudev/gudevmarshal.list \
	gudev/gudevprivate.h \
	gudev/gudevenumtypes.h.template \
	gudev/gudevenumtypes.c.template \
	gudev/gjs-example.js \
	gudev/seed-example-enum.js \
	gudev/seed-example.js

lib_LTLIBRARIES += \
	libgudev-1.0.la

pkgconfiglib_DATA += \
	gudev/gudev-1.0.pc

CLEANFILES += \
	gudev/gudev-1.0.pc

libgudev_1_0_la_SOURCES = \
	gudev/gudevenumtypes.c \
	gudev/gudevclient.c \
	gudev/gudevdevice.c \
	gudev/gudevenumerator.c

nodist_libgudev_1_0_la_SOURCES =  \
	gudev/gudevmarshal.h \
	gudev/gudevmarshal.c

libgudev_1_0_la_CPPFLAGS = \
	$(AM_CPPFLAGS) \
	-I$(top_srcdir)/src\
	-I$(top_builddir)/src\
	-I$(top_srcdir)/src/libudev \
	-I$(top_builddir)/src/gudev \
	-I$(top_srcdir)/src/gudev \
	-D_POSIX_PTHREAD_SEMANTICS -D_REENTRANT \
	-D_GUDEV_COMPILATION \
	-DG_LOG_DOMAIN=\"GUdev\"

libgudev_1_0_la_CFLAGS = \
	$(AM_CFLAGS) \
	-fvisibility=default \
	$(GLIB_CFLAGS)

libgudev_1_0_la_LIBADD = \
	libudev.la \
	$(GLIB_LIBS)

libgudev_1_0_la_LDFLAGS = \
	$(AM_LDFLAGS) \
	-version-info $(LIBGUDEV_CURRENT):$(LIBGUDEV_REVISION):$(LIBGUDEV_AGE) \
	-export-dynamic -no-undefined \
	-export-symbols-regex '^g_udev_.*'

BUILT_SOURCES = \
	gudev/gudevenumtypes.h \
	gudev/gudevmarshal.h \
	$(nodist_libgudev_1_0_la_SOURCES)


gudev/gudevmarshal.h: gudev/gudevmarshal.list
	$(AM_V_at)$(MKDIR_P) $(dir $@)
	$(AM_V_GEN)glib-genmarshal $< --prefix=g_udev_marshal --header > $@

gudev/gudevclient.c: gudev/gudevmarshal.h

gudev/gudevmarshal.c: gudev/gudevmarshal.list
	$(AM_V_at)$(MKDIR_P) $(dir $@)
	$(AM_V_GEN)echo '#include "gudevmarshal.h"' > $@ && \
	glib-genmarshal $< --prefix=g_udev_marshal --body >> $@

gudev/gudevenumtypes.h: gudev/gudevenumtypes.h.template gudev/gudevenums.h
	$(AM_V_at)$(MKDIR_P) $(dir $@)
	$(AM_V_GEN)glib-mkenums --template $^ > $@

gudev/gudevenumtypes.c: gudev/gudevenumtypes.c.template gudev/gudevenums.h gudev/gudevenumtypes.h
	$(AM_V_at)$(MKDIR_P) $(dir $@)
	$(AM_V_GEN)glib-mkenums --template $^ > $@

CLEANFILES += \
	gudev/gudevmarshal.h \
	gudev/gudevmarshal.c \
	gudev/gudevenumtypes.h \
	gudev/gudevenumtypes.c

if HAVE_INTROSPECTION
-include $(INTROSPECTION_MAKEFILE)

GUdev-1.0.gir: \
	libgudev-1.0.la

GUdev_1_0_gir_INCLUDES = \
	GObject-2.0

GUdev_1_0_gir_CFLAGS = \
	$(INCLUDES) \
	-D_GUDEV_COMPILATION \
	-D_GUDEV_WORK_AROUND_DEV_T_BUG \
	-I$(top_srcdir)/src \
	-I$(top_builddir)/src \
	-I$(top_srcdir)/src/gudev \
	-I$(top_builddir)/src/gudev

GUdev_1_0_gir_LIBS = \
	libgudev-1.0.la \
	libudev-core.la

GUdev_1_0_gir_SCANNERFLAGS = \
	--pkg-export=gudev-1.0 \
	--warn-all

GUdev_1_0_gir_FILES = \
	gudev/gudev.h \
	gudev/gudevtypes.h \
	gudev/gudevenums.h \
	gudev/gudevenumtypes.h \
	gudev/gudevclient.h \
	gudev/gudevdevice.h \
	gudev/gudevenumerator.h \
	gudev/gudevdevice.c \
	gudev/gudevenumerator.c

INTROSPECTION_GIRS = \
	GUdev-1.0.gir

INTROSPECTION_SCANNER_ARGS = \
	--c-include=gudev.h

gir_DATA = \
	GUdev-1.0.gir

typelibs_DATA = \
	GUdev-1.0.typelib

CLEANFILES += \
	$(gir_DATA) $(typelibs_DATA)

endif # HAVE_INTROSPECTION

# move lib from $(libdir) to $(rootlibdir) and update devel link, if needed
libgudev-install-move-hook:
	if test "$(libdir)" != "$(rootlibdir)"; then \
		$(MKDIR_P) $(DESTDIR)$(rootlibdir) && \
		so_img_name=$$(readlink $(DESTDIR)$(libdir)/libgudev-1.0.so) && \
		so_img_rel_target_prefix=$$(echo $(libdir) | sed 's,\(^/\|\)[^/][^/]*,..,g') && \
		ln -sf $$so_img_rel_target_prefix$(rootlibdir)/$$so_img_name $(DESTDIR)$(libdir)/libgudev-1.0.so && \
		mv $(DESTDIR)$(libdir)/libgudev-1.0.so.* $(DESTDIR)$(rootlibdir); \
	fi

libgudev-uninstall-move-hook:
	rm -f $(DESTDIR)$(rootlibdir)/libgudev-1.0.so*

INSTALL_EXEC_HOOKS = \
	libgudev-install-move-hook

UNINSTALL_EXEC_HOOKS = \
	libgudev-uninstall-move-hook
endif #GUDEV

if ENABLE_KEYMAP
udevlibexec_PROGRAMS += \
	keymap/keymap

keymap_keymap_SOURCES = \
	keymap/keymap.c

nodist_keymap_keymap_SOURCES = \
	keymap/keys-from-name.h \
	keymap/keys-to-name.h

keymap_keymap_CPPFLAGS = \
	$(AM_CPPFLAGS)

keymap_keymap_LDADD = \
	libudev-core.la

BUILT_SOURCES = \
	$(nodist_keymap_keymap_SOURCES)

dist_doc_DATA = \
	keymap/README.keymap.txt

dist_udevlibexec_SCRIPTS = \
	keymap/findkeyboards \
	keymap/keyboard-force-release.sh

TESTS = \
	keymap/check-keymaps.sh

CLEANFILES += \
	keymap/keys.txt \
	keymap/keys-from-name.gperf \
	kyemaps/keyboard-force-release.sh \
	$(BUILT_SOURCES)

EXTRA_DIST += \
	keymap/check-keymaps.sh \
	keymap/keyboard-force-release.sh.in

keymap/keys.txt: Makefile
	$(AM_V_at)$(MKDIR_P) $(dir $@)
	$(AM_V_GEN)$(CPP) $(CFLAGS) $(AM_CPPFLAGS) $(CPPFLAGS) -dM -include linux/input.h - < /dev/null | $(AWK) '/^#define[ \t]+KEY_[^ ]+[ \t]+[0-9]/ { if ($$2 != "KEY_MAX") { print $$2 } }' | sed 's/^KEY_COFFEE$$/KEY_SCREENLOCK/' > $@

keymap/keys-from-name.gperf: keymap/keys.txt Makefile
	$(AM_V_GEN)$(AWK) 'BEGIN{ print "struct key { const char* name; unsigned short id; };"; print "%null-strings"; print "%%";} { print $$1 ", " $$1 }' < $< > $@

keymap/keys-from-name.h: keymap/keys-from-name.gperf Makefile
	$(AM_V_GEN)$(GPERF) -L ANSI-C -t --ignore-case -N lookup_key -H hash_key_name -p -C < $< > $@

keymap/keys-to-name.h: keymap/keys.txt Makefile
	$(AM_V_GEN)$(AWK) 'BEGIN{ print "const char* const key_names[KEY_CNT] = { "} { print "[" $$1 "] = \"" $$1 "\"," } END{print "};"}' < $< > $@
endif #KEYMAP

if ENABLE_LEGACYLIB

define move-legacylib
	$(MKDIR_P) $(DESTDIR)$(rootlibdir) && \
	mv $(DESTDIR)$(libdir)/$$libname $(DESTDIR)$(rootlibdir)/libudev.so.0
endef

lib_LTLIBRARIES += \
	libudev-legacy.la

libudev_legacy_la_SOURCES = \
	libudev-legacy/libudev.c \
	libudev-legacy/libudev-list.c \
	libudev-legacy/libudev-util.c \
	libudev-legacy/libudev-device.c \
	libudev-legacy/libudev-enumerate.c \
	libudev-legacy/libudev-monitor.c \
	libudev-legacy/libudev-queue.c

libudev_legacy_la_CFLAGS = \
	$(AM_CFLAGS) \
	-shared

noinst_HEADERS += \
	libudev-legacy/libudev.h \
	libudev-legacy/libudev-private.h

libudev_legacy_la_LDFLAGS = \
	$(AM_LDFLAGS) \
	-avoid-version

#install-exec-hook:
#	libname=libudev-legacy.so && $(move-legacylib)

EXTRA_DIST += \
	COPYING
endif #legacy-libudev
