# policy build
# Copyright (c) 2023-2024 Michał Zagórski (zagura)
# SPDX-License-Identifier: GPL-2.0-or-later

add_subdirectory(common)
add_subdirectory(backend)
set(POLICY_SRCS "")

bison_target(policyParser policy.yy ${CMAKE_CURRENT_BINARY_DIR}/y.policy_parser_tab.cc
             COMPILE_FLAGS "-d -ppolicy_parser"
             VERBOSE
             DEFINES_FILE ${CMAKE_CURRENT_BINARY_DIR}/y.policy_parser_tab.hh)


flex_target(policyScanner policy.ll
                         ${CMAKE_CURRENT_BINARY_DIR}/lex.policy_parser.cc
                         COMPILE_FLAGS "-Ppolicy_parser"
                         DEFINES_FILE ${CMAKE_CURRENT_BINARY_DIR}/lex.policy_parser.hh)
ADD_FLEX_BISON_DEPENDENCY(policyScanner policyParser)

set_source_files_properties(
                            ${BISON_policyParser_OUTPUTS}
                            ${FLEX_policyScanner_OUTPUTS}
                            PROPERTIES
                            COMPILE_FLAGS -Wno-sign-compare
                            COMPILE_FLAGS -Wno-effc++)

add_executable(xorp_policy
                     ${BISON_policyParser_OUTPUTS}
                     ${FLEX_policyScanner_OUTPUTS}
                     code.cc
                     code_generator.cc
                     code_list.cc
                     configuration.cc
                     dependency.cc
                     export_code_generator.cc
                     filter_manager.cc
                     parser.cc
                     policy_list.cc
                     policy_map.cc
                     policy_statement.cc
                     policy_target.cc
                     process_watch.cc
                     protocol_map.cc
                     semantic_varrw.cc
                     set_map.cc
                     source_match_code_generator.cc
                     term.cc
                     test_varrw.cc
                     var_map.cc
                     visitor_dep.cc
                     visitor_printer.cc
                     visitor_semantic.cc
                     visitor_test.cc
                     xrl_target.cc
                     xorp_policy.cc)

target_link_libraries(xorp_policy policy_backend
                                  policy_common
                                  tgts_policy
                                  xif_policy_backend
                                  xif_finder_event_notifier
                                  xif_rib
                                  xipc
                                  xorp
                                  proto
                                  comm
                                  )

install(TARGETS xorp_policy RUNTIME DESTINATION ${XORP_MODULEDIR})

if (RTLD_ORIGIN)
    set_target_properties(xorp_policy PROPERTIES BUILD_RPATH "$ORIGIN:$ORIGIN/../lib"
                                                 INSTALL_RPATH "$ORIGIN:$ORIGIN/../lib")
endif()
if (BUILDDIR_RUN)
    add_custom_command(TARGET xorp_policy POST_BUILD COMMAND ${CMAKE_COMMAND} -E create_symlink $<TARGET_FILE:xorp_policy>
                        ${XORP_ALIAS_MODULEDIR}/$<TARGET_FILE_NAME:xorp_policy>)
endif()
