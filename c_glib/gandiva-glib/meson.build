# -*- indent-tabs-mode: nil -*-
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.

project_name = 'gandiva-glib'

sources = files(
    'expression.cpp',
    'filter.cpp',
    'function-registry.cpp',
    'function-signature.cpp',
    'native-function.cpp',
    'node.cpp',
    'projector.cpp',
    'selection-vector.cpp',
)

c_headers = files(
    'expression.h',
    'filter.h',
    'function-registry.h',
    'function-signature.h',
    'gandiva-glib.h',
    'native-function.h',
    'node.h',
    'projector.h',
    'selection-vector.h',
)

cpp_headers = files(
    'expression.hpp',
    'filter.hpp',
    'function-signature.hpp',
    'gandiva-glib.hpp',
    'native-function.hpp',
    'node.hpp',
    'projector.hpp',
    'selection-vector.hpp',
)

version_h = configure_file(
    input: 'version.h.in',
    output: 'version.h',
    command: [
        python3,
        generate_version_header_py,
        '--library',
        'GGANDIVA',
        '--version',
        version,
        '--input',
        '@INPUT@',
        '--output',
        '@OUTPUT@',
    ],
)

c_headers += version_h

enums = gnome.mkenums(
    'enums',
    sources: c_headers,
    identifier_prefix: 'GGandiva',
    symbol_prefix: 'ggandiva',
    c_template: 'enums.c.template',
    h_template: 'enums.h.template',
    install_dir: join_paths(include_dir, 'gandiva-glib'),
    install_header: true,
)
enums_source = enums[0]
enums_header = enums[1]

headers = c_headers + cpp_headers
install_headers(headers, subdir: project_name)

dependencies = [gandiva, arrow_glib]
libgandiva_glib = library(
    'gandiva-glib',
    sources: sources + enums,
    install: true,
    dependencies: dependencies,
    implicit_include_directories: false,
    include_directories: base_include_directories,
    cpp_args: ['-DGGANDIVA_COMPILATION'],
    c_args: ['-DGGANDIVA_COMPILATION'],
    soversion: so_version,
    version: library_version,
)
gandiva_glib = declare_dependency(
    link_with: libgandiva_glib,
    include_directories: base_include_directories,
    dependencies: dependencies,
    sources: enums_header,
)

pkgconfig.generate(
    libgandiva_glib,
    description: 'C API for Apache Arrow Gandiva based on GLib',
    filebase: project_name,
    name: 'Apache Arrow Gandiva GLib',
    requires: ['gandiva', 'arrow-glib'],
    variables: pkgconfig_variables,
    version: version,
)

if have_gi
    gandiva_glib_gir = gnome.generate_gir(
        libgandiva_glib,
        dependencies: declare_dependency(sources: arrow_glib_gir),
        export_packages: 'gandiva-glib',
        extra_args: [
            '--warn-all',
            '--include-uninstalled=./arrow-glib/Arrow-1.0.gir',
        ],
        header: 'gandiva-glib/gandiva-glib.h',
        identifier_prefix: 'GGandiva',
        includes: ['Arrow-1.0'],
        kwargs: generate_gi_common_args,
        namespace: 'Gandiva',
        sources: sources + c_headers + enums,
        symbol_prefix: 'ggandiva',
    )

    if generate_vapi
        gnome.generate_vapi(
            'gandiva-glib',
            install: true,
            packages: [arrow_glib_vapi, 'gio-2.0'],
            sources: [gandiva_glib_gir[0]],
        )
    endif
endif
