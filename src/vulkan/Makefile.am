include Makefile.sources

noinst_LTLIBRARIES = libvulkan_wsi.la libvulkan_util.la

vulkan_includedir = $(includedir)/vulkan
vulkan_api_xml = $(top_srcdir)/src/vulkan/registry/vk.xml
vk_android_native_buffer_xml = $(top_srcdir)/src/vulkan/registry/vk_android_native_buffer.xml

MKDIR_GEN = $(AM_V_at)$(MKDIR_P) $(@D)
PYTHON_GEN = $(AM_V_GEN)$(PYTHON2) $(PYTHON_FLAGS)

EXTRA_DIST = \
	$(top_srcdir)/include/vulkan/vk_android_native_buffer.h \
	util/gen_enum_to_str.py

VULKAN_UTIL_SOURCES = \
	$(VULKAN_UTIL_FILES) \
	$(VULKAN_UTIL_GENERATED_FILES)

BUILT_SOURCES = \
	$(VULKAN_UTIL_GENERATED_FILES)

util/vk_enum_to_str.c util/vk_enum_to_str.h: util/gen_enum_to_str.py \
		$(vulkan_api_xml) $(vk_android_native_buffer_xml)
	$(MKDIR_GEN)
	$(PYTHON_GEN) $(srcdir)/util/gen_enum_to_str.py \
	    --xml $(vulkan_api_xml) \
	    --xml $(vk_android_native_buffer_xml) \
	    --outdir $(top_builddir)/src/vulkan/util

libvulkan_util_la_SOURCES = $(VULKAN_UTIL_SOURCES)

AM_CPPFLAGS = \
	$(DEFINES) \
	-I$(top_srcdir)/include \
	-I$(top_srcdir)/src \
	-I$(top_srcdir)/src/vulkan/util \
	-I$(top_srcdir)/src/gallium/auxiliary \
	-I$(top_srcdir)/src/gallium/include

AM_CFLAGS = \
	$(LIBDRM_CFLAGS) \
	$(VISIBILITY_CFLAGS)

VULKAN_WSI_SOURCES = \
	$(VULKAN_WSI_FILES)

if HAVE_PLATFORM_X11
AM_CPPFLAGS += \
	$(XCB_DRI3_CFLAGS) \
	-DVK_USE_PLATFORM_XCB_KHR \
	-DVK_USE_PLATFORM_XLIB_KHR

VULKAN_WSI_SOURCES += $(VULKAN_WSI_X11_FILES)
endif

BUILT_SOURCES += $(VULKAN_WSI_WAYLAND_GENERATED_FILES)
CLEANFILES = $(BUILT_SOURCES)

WL_DRM_XML = $(top_srcdir)/src/egl/wayland/wayland-drm/wayland-drm.xml

wsi/wayland-drm-protocol.c : $(WL_DRM_XML)
	$(MKDIR_GEN)
	$(AM_V_GEN)$(WAYLAND_SCANNER) code < $< > $@

wsi/wayland-drm-client-protocol.h : $(WL_DRM_XML)
	$(MKDIR_GEN)
	$(AM_V_GEN)$(WAYLAND_SCANNER) client-header < $< > $@

if HAVE_PLATFORM_WAYLAND
AM_CPPFLAGS += \
	-I$(top_builddir)/src/vulkan/wsi \
	$(WAYLAND_CLIENT_CFLAGS) \
	-DVK_USE_PLATFORM_WAYLAND_KHR

VULKAN_WSI_SOURCES += \
	$(VULKAN_WSI_WAYLAND_FILES) \
	$(VULKAN_WSI_WAYLAND_GENERATED_FILES)

endif

libvulkan_wsi_la_SOURCES = $(VULKAN_WSI_SOURCES)
