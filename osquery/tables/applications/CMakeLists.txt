# Copyright (c) 2014-present, The osquery authors
#
# This source code is licensed as defined by the LICENSE file found in the
# root directory of this source tree.
#
# SPDX-License-Identifier: (Apache-2.0 OR GPL-2.0-only)

function(osqueryTablesApplicationsMain)

  if(OSQUERY_BUILD_TESTS)
    add_subdirectory("posix/tests")
  endif()

  generateOsqueryTablesApplications()
endfunction()

function(generateOsqueryTablesApplications)
  set(source_files
    browser_chrome.cpp
    browser_utils.cpp
    browser_firefox.cpp
    atom_packages.cpp
  )

  if(DEFINED PLATFORM_POSIX)
    list(APPEND source_files
      posix/browser_opera.cpp
      posix/carbon_black.cpp
      posix/docker.cpp
      posix/lxd.cpp
      posix/prometheus_metrics.cpp
    )
  endif()

  if(DEFINED PLATFORM_MACOS)
    list(APPEND source_files
      darwin/browser_plugins.cpp
    )
  elseif(DEFINED PLATFORM_WINDOWS)
    list(APPEND source_files
      windows/carbon_black.cpp
      windows/office_mru.cpp
    )
  endif()

  add_osquery_library(osquery_tables_applications EXCLUDE_FROM_ALL
    ${source_files}
  )

  target_link_libraries(osquery_tables_applications PUBLIC
    osquery_cxx_settings
    osquery_config
    osquery_filesystem
    osquery_tables_system_systemtable
    osquery_utils_conversions
    osquery_utils_info
    osquery_utils_json
    plugins_config_parsers
    thirdparty_boost
  )

  set(public_header_files
    browser_utils.h
  )

  if(DEFINED PLATFORM_POSIX)
    list(APPEND public_header_files
      posix/prometheus_metrics.h
    )
  endif()

  generateIncludeNamespace(osquery_tables_applications "osquery/tables/applications" "FULL_PATH" ${public_header_files})

  if(DEFINED PLATFORM_POSIX)
    add_test(NAME osquery_tables_applications_posix_tests_prometheusmetricstests-test COMMAND osquery_tables_applications_posix_tests_prometheusmetricstests-test)
  endif()
endfunction()

osqueryTablesApplicationsMain()
