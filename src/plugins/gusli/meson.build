# SPDX-FileCopyrightText: Copyright (c) 2025-2026 NVIDIA CORPORATION & AFFILIATES. All rights reserved.
# SPDX-License-Identifier: Apache-2.0
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Mandatory dependencies
cpp_sources = ['gusli_plugin.cpp', 'gusli_backend.cpp', 'gusli_backend.h']
compile_defs = []
compile_flags = []
gusli_plugin_inc_list = [nixl_inc_dirs, utils_inc_dirs]
gusli_link_args = ['-l' + gusli_lib_file]
if gusli_prefix_resolved != ''
    gusli_plugin_inc_list += include_directories(join_paths(gusli_prefix_resolved, 'include'), is_system: true)
    gusli_link_args = ['-L' + join_paths(gusli_prefix_resolved, 'lib'), '-l' + gusli_lib_file]
endif
gusli_deps = [ nixl_infra,
               declare_dependency(link_args: gusli_link_args),
               absl_log_dep]

if 'GUSLI' in static_plugins
  gusli_backend_lib = static_library('GUSLI', cpp_sources,
        dependencies: gusli_deps,
        include_directories: gusli_plugin_inc_list,
        install: false,
        cpp_args: compile_defs + compile_flags,
        name_prefix: 'libplugin_')  # Custom prefix for plugin libraries
else
  gusli_backend_lib = shared_library('GUSLI', cpp_sources,
        dependencies: gusli_deps,
        include_directories: gusli_plugin_inc_list,
        install: true,
        cpp_args: compile_defs + ['-fPIC'],
        name_prefix: 'libplugin_',  # Custom prefix for plugin libraries
        install_dir: plugin_install_dir,
        install_rpath: '$ORIGIN/..')
  if get_option('buildtype') == 'debug'
        run_command('sh', '-c',
                    'echo "GUSLI=' + gusli_backend_lib.full_path() + '" >> ' + plugin_build_dir + '/pluginlist',
                    check: true
                )
    endif
endif

gusli_backend_interface = declare_dependency(link_with: gusli_backend_lib)
