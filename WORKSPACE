load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "io_tweag_rules_nixpkgs",
    strip_prefix = "rules_nixpkgs-0.7.0",
    urls = ["https://github.com/tweag/rules_nixpkgs/archive/v0.7.0.zip"],
    sha256 = "d4582d6492d8bf0c111e2869bfa4c4b80767e77e5ed5f2ffaaac854c816c9e55",
)

load(
    "@io_tweag_rules_nixpkgs//nixpkgs:repositories.bzl",
    "rules_nixpkgs_dependencies"
    )

rules_nixpkgs_dependencies()

load(
    "@io_tweag_rules_nixpkgs//nixpkgs:nixpkgs.bzl",
    "nixpkgs_cc_configure",
    "nixpkgs_git_repository",
    "nixpkgs_local_repository",
    "nixpkgs_package",
    "nixpkgs_python_configure",
    "nixpkgs_sh_posix_configure"
    )

# this would probably need to be a local mirror of "nixpkgs" with our addons.
nixpkgs_git_repository(
    name = "nixpkgs",
    revision = "20.03",
    sha256 = "f21ca8bc4c8f848a351232e09f3a58d280c05323173a78a5a6013937fb05c6fe"
)

nixpkgs_cc_configure(repository = "@nixpkgs")

# Glib headers required from GTK
GLIB_DEV_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "includes",
    hdrs = glob(["include/**/*.h"]),
    includes = [
        "include",
        "include/gio-unix-2.0",
        "include/glib-2.0",
    ],
)
'''

nixpkgs_package(
    name = "glib.dev",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = GLIB_DEV_BUILD,
)

GLIB_OUT_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

# "glibconfig.h" is on the "out" package.
cc_library(
    name = "includes",
    hdrs = glob(["lib/**/*.h"]),
    strip_include_prefix = "lib/glib-2.0/include"
)

# I don't use the libraries directly, letting NIX solve this.
cc_library(
    name = "gio-so",
    srcs = glob(["lib/libgio*.so"], allow_empty = False),
)

#cc_library(
#    name = "glib-so",
#    srcs = glob(["lib/libglib*.so"], allow_empty = False),
#    deps = [":includes"]
#)
#
#cc_library(
#    name = "gmodule-so",
#    srcs = glob(["lib/libgmodule*.so"], allow_empty = False),
#)
#
cc_library(
    name = "gobject-so",
    srcs = glob(["lib/libgobject*.so"], allow_empty = False),
)
#
#cc_library(
#    name = "gthread-so",
#    srcs = glob(["lib/libgthread*.so"], allow_empty = False),
#)
'''

nixpkgs_package(
    name = "glib.out",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = GLIB_OUT_BUILD,
)

# harfbuzz headers required from pango
HARFBUZZ_DEV_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "includes",
    hdrs = glob(["include/**/*.h"]),
    strip_include_prefix = "include/harfbuzz",
)
'''

nixpkgs_package(
    name = "harfbuzz.dev",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = HARFBUZZ_DEV_BUILD,
)

# Pango headers required from GTK
PANGO_DEV_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "includes",
    hdrs = glob(["include/**/*.h"]),
    strip_include_prefix = "include/pango-1.0",
    deps = ["@harfbuzz.dev//:includes"]
)
'''

nixpkgs_package(
    name = "pango.dev",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = PANGO_DEV_BUILD,
)

# Cairo headers required from GTK
CAIRO_DEV_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "includes",
    hdrs = glob(["include/**/*.h"]),
    strip_include_prefix = "include/cairo",
)
'''

nixpkgs_package(
    name = "cairo.dev",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = CAIRO_DEV_BUILD,
)

# gdk pixbuf headers required from GTK
GDK_PIXBUF_DEV_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "includes",
    hdrs = glob(["include/**/*.h"]),
    strip_include_prefix = "include/gdk-pixbuf-2.0",
)
'''

nixpkgs_package(
    name = "gdk-pixbuf.dev",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = GDK_PIXBUF_DEV_BUILD,
)

# atk headers required from GTK
ATK_DEV_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "includes",
    hdrs = glob(["include/**/*.h"]),
    strip_include_prefix = "include/atk-1.0",
)
'''

nixpkgs_package(
    name = "atk.dev",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = ATK_DEV_BUILD,
)

GTK3_X11_DEV_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "includes",
    hdrs = glob(["include/**/*.h"]),
    includes =  [
        "include",
        "include/gtk-3.0",
    ],
    deps = [
        "@glib.dev//:includes",
        "@glib.out//:includes",
        "@pango.dev//:includes",
        "@cairo.dev//:includes",
        "@gdk-pixbuf.dev//:includes",
        "@atk.dev//:includes",
        "@glib.out//:gio-so",
        "@glib.out//:gobject-so",
    ],
)
'''

nixpkgs_package(
    name = "gtk3-x11.dev",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = GTK3_X11_DEV_BUILD,
)

GTK3_X11_BUILD = '''
load("@rules_cc//cc:defs.bzl", "cc_library")
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "gtk3-so",
    srcs = glob(["lib/**/*.so"]),
    deps = ["@gtk3-x11.dev//:includes"],
)
'''

nixpkgs_package(
    name = "gtk3-x11",
    repositories = { "nixpkgs": "@nixpkgs//:default.nix" },
    build_file_content = GTK3_X11_BUILD,
)
