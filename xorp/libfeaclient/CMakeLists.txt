# libfeaclient build
# Copyright (c) 2023-2024 Michał Zagórski (zagura)
# SPDX-License-Identifier: GPL-2.0-or-later

if(TESTS_PROGRAMS)
    add_subdirectory(tests)
endif()

add_library(fea_client
                      ifmgr_atoms.cc
                      ifmgr_cmds.cc
                      ifmgr_cmd_queue.cc
                      ifmgr_xrl_replicator.cc
                      ifmgr_xrl_mirror.cc)

target_link_libraries(fea_client  xif_fea_ifmgr_mirror
                                  xif_fea_ifmgr_replicator
                                  tgts_fea_ifmgr_mirror
                                  xorp
                                  xipc
                                  comm
                                  proto)

install(TARGETS fea_client LIBRARY DESTINATION ${XORP_LIBDIR})


if (RTLD_ORIGIN)
    set_target_properties(fea_client PROPERTIES BUILD_RPATH "$ORIGIN:$ORIGIN/../lib"
                                                INSTALL_RPATH "$ORIGIN:$ORIGIN/../lib")
endif()

if (BUILDDIR_RUN)
    add_custom_command(TARGET fea_client POST_BUILD COMMAND ${CMAKE_COMMAND} -E create_symlink $<TARGET_FILE:fea_client>
                        ${XORP_ALIAS_LIBDIR}/$<TARGET_FILE_NAME:fea_client>)
endif()
