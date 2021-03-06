## Process this file with automake to produce Makefile.in

libgimpbase = $(top_builddir)/libgimpbase/libgimpbase-$(GIMP_API_VERSION).la
libgimpconfig = $(top_builddir)/libgimpconfig/libgimpconfig-$(GIMP_API_VERSION).la
libgimpcolor = $(top_builddir)/libgimpcolor/libgimpcolor-$(GIMP_API_VERSION).la
libgimpmath = $(top_builddir)/libgimpmath/libgimpmath-$(GIMP_API_VERSION).la
libgimpmodule = $(top_builddir)/libgimpmodule/libgimpmodule-$(GIMP_API_VERSION).la
libgimpthumb = $(top_builddir)/libgimpthumb/libgimpthumb-$(GIMP_API_VERSION).la

AM_CPPFLAGS = \
	-DG_LOG_DOMAIN=\"Gimp-Config\"				\
	-DGIMP_APP_VERSION_STRING=\"$(GIMP_APP_VERSION)\"	\
	-I$(top_builddir)					\
	-I$(top_srcdir)						\
	-I$(top_builddir)/app					\
	-I$(top_srcdir)/app					\
	$(GIO_UNIX_CFLAGS)					\
	$(GIO_WINDOWS_CFLAGS)					\
	$(GEGL_CFLAGS)						\
	$(CAIRO_CFLAGS)						\
	$(GDK_PIXBUF_CFLAGS)					\
	-I$(includedir)

noinst_LIBRARIES = libappconfig.a

libappconfig_a_sources = \
	config-enums.h			\
	config-types.h			\
	gimpconfig-dump.c		\
	gimpconfig-dump.h		\
	gimpconfig-file.c		\
	gimpconfig-file.h		\
	gimpconfig-utils.c		\
	gimpconfig-utils.h		\
	gimpcoreconfig.c		\
	gimpcoreconfig.h		\
	gimpdisplayconfig.c		\
	gimpdisplayconfig.h		\
	gimpdisplayoptions.c		\
	gimpdisplayoptions.h		\
	gimpgeglconfig.c		\
	gimpgeglconfig.h		\
	gimpguiconfig.c			\
	gimpguiconfig.h			\
	gimppluginconfig.c		\
	gimppluginconfig.h		\
	gimprc.c			\
	gimprc.h			\
	gimprc-blurbs.h			\
	gimprc-deserialize.c		\
	gimprc-deserialize.h		\
	gimprc-serialize.c		\
	gimprc-serialize.h		\
	gimprc-unknown.c		\
	gimprc-unknown.h		\
	gimpxmlparser.c			\
	gimpxmlparser.h

libappconfig_a_built_sources = \
	config-enums.c

libappconfig_a_SOURCES = \
	$(libappconfig_a_built_sources)	\
	$(libappconfig_a_sources)

EXTRA_PROGRAMS = test-config

#
# unit tests for the GimpConfig system
#

TESTS = test-config

test_config_DEPENDENCIES = $(gimpconfig_libs)

# We need this due to circular dependencies, see more detailed
# comments about it in app/Makefile.am
test_config_LDFLAGS = \
	-Wl,-u,$(SYMPREFIX)gimp_cairo_image_surface_create_buffer	\
	-Wl,-u,$(SYMPREFIX)xcf_init					\
	-Wl,-u,$(SYMPREFIX)gimp_pdb_new					\
	-Wl,-u,$(SYMPREFIX)internal_procs_init				\
	-Wl,-u,$(SYMPREFIX)gimp_plug_in_manager_restore			\
	-Wl,-u,$(SYMPREFIX)plug_in_icc_profile_apply_rgb		\
	-Wl,-u,$(SYMPREFIX)gimp_vectors_undo_get_type			\
	-Wl,-u,$(SYMPREFIX)gimp_vectors_mod_undo_get_type		\
	-Wl,-u,$(SYMPREFIX)gimp_vectors_prop_undo_get_type		\
	-Wl,-u,$(SYMPREFIX)gimp_image_map_config_get_type		\
	-Wl,-u,$(SYMPREFIX)erode_region

test_config_LDADD = \
	../xcf/libappxcf.a			\
	../pdb/libappinternal-procs.a		\
	../pdb/libapppdb.a			\
	../plug-in/libappplug-in.a		\
	../vectors/libappvectors.a		\
	../core/libappcore.a			\
	../file/libappfile.a			\
	../text/libapptext.a			\
	../paint/libapppaint.a			\
	../gegl/libappgegl.a			\
	../operations/libappoperations.a	\
	libappconfig.a				\
	../gimp-debug.o				\
	../gimp-log.o				\
	$(libgimpmodule)			\
	$(libgimpcolor)				\
	$(libgimpthumb)				\
	$(libgimpmath)				\
	$(libgimpconfig)			\
	$(libgimpbase)				\
	$(LCMS_LIBS)				\
	$(PANGOCAIRO_LIBS)			\
	$(HARFBUZZ_LIBS)			\
	$(GDK_PIXBUF_LIBS)			\
	$(GEGL_LIBS)				\
	$(GIO_LIBS)				\
	$(GEXIV2_LIBS)				\
	$(Z_LIBS)				\
	$(JSON_C_LIBS)				\
	$(LIBMYPAINTGEGL_LIBS)

CLEANFILES = $(EXTRA_PROGRAMS) foorc

#
# rules to generate built sources
#
# setup autogeneration dependencies
gen_sources = xgen-dec
CLEANFILES += $(gen_sources)

config-enums.c: $(srcdir)/config-enums.h $(GIMP_MKENUMS)
	$(GIMP_MKENUMS) \
		--fhead "#include \"config.h\"\n#include <gio/gio.h>\n#include \"libgimpbase/gimpbase.h\"\n#include \"config-enums.h\"\n#include\"gimp-intl.h\"" \
		--fprod "\n/* enumerations from \"@filename@\" */" \
		--vhead "GType\n@enum_name@_get_type (void)\n{\n  static const G@Type@Value values[] =\n  {" \
		--vprod "    { @VALUENAME@, \"@VALUENAME@\", \"@valuenick@\" }," \
		--vtail "    { 0, NULL, NULL }\n  };\n" \
		--dhead "  static const Gimp@Type@Desc descs[] =\n  {" \
		--dprod "    { @VALUENAME@, @valuedesc@, @valuehelp@ }," \
		--dtail "    { 0, NULL, NULL }\n  };\n\n  static GType type = 0;\n\n  if (G_UNLIKELY (! type))\n    {\n      type = g_@type@_register_static (\"@EnumName@\", values);\n      gimp_type_set_translation_context (type, \"@enumnick@\");\n      gimp_@type@_set_value_descriptions (type, descs);\n    }\n\n  return type;\n}\n" \
		$(srcdir)/config-enums.h > xgen-dec \
	&& cp xgen-dec $(@F) \
	&& rm -f xgen-dec

