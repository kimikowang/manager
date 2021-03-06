load("@bazel_tools//tools/build_defs/pkg:pkg.bzl", "pkg_tar")
load("//docker:manager_docker.bzl", "manager_docker_build")

# Use "manual" target tag to skip rules in the wildcard expansion

pkg_tar(
    name = "manager",
    extension = "tar.gz",
    files = [
        "//cmd/manager",
    ],
    mode = "0755",
    package_dir = "/usr/local/bin/",
    tags = ["manual"],
)

pkg_tar(
    name = "test",
    extension = "tar.gz",
    files = [
        "//test/client",
        "//test/server",
    ],
    mode = "0755",
    package_dir = "/bin",
    tags = ["manual"],
)

pkg_tar(
    name = "config",
    extension = "tar.gz",
    files = ["envoy"],
    mode = "0666",
    modes = {"envoy": "777"},
    package_dir = "/etc",
    tags = ["manual"],
)

pkg_tar(
    name = "prepare_proxy",
    extension = "tar.gz",
    files = [
        "prepare_proxy.sh",
    ],
    mode = "0755",
    package_dir = "/usr/local/bin/",
    tags = ["manual"],
)

manager_docker_build(
    debs = [
        "@deb_iptables//file",
        "@deb_libnfnetlink//file",
        "@deb_libxtables//file",
    ],
    entrypoint = ["/usr/local/bin/prepare_proxy.sh"],
    images = [
        {
            "name": "init",
            "base": "@docker_ubuntu//:xenial",
        },
        {
            "name": "init_debug",
            "base": "@ubuntu_xenial_debug//file",
        },
    ],
    repository = "istio",
    tags = ["manual"],
    tars = [":prepare_proxy"],
)

manager_docker_build(
    entrypoint = ["/usr/local/bin/manager"],
    images = [
        {
            "name": "runtime",
            "base": "@docker_ubuntu//:xenial",
            "tars": [
                "@istio_proxy//file",
            ],
        },
        {
            "name": "runtime_debug",
            "base": "@ubuntu_xenial_debug//file",
            "tars": [
                "@istio_proxy_debug//file",
            ],
        },
    ],
    repository = "istio",
    tags = ["manual"],
    tars = [
        ":manager",
        ":config",
    ],
)

manager_docker_build(
    entrypoint = ["/bin/server"],
    images = [
        {
            "name": "app",
            "base": "@docker_ubuntu//:xenial",
        },
        {
            "name": "app_debug",
            "base": "@ubuntu_xenial_debug//file",
        },
    ],
    repository = "istio",
    tags = ["manual"],
    tars = [":test"],
)
