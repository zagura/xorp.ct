# fea/data_plane/firewall build
# Copyright (c) 2023-2024 Michał Zagórski (zagura)
# SPDX-License-Identifier: GPL-2.0-or-later

add_library(firewall OBJECT
                     firewall_get_pf.cc
                     firewall_get_dummy.cc
                     firewall_get_ipfw2.cc
                     firewall_get_netfilter.cc
                     firewall_set_dummy.cc
                     firewall_set_ipfw2.cc
                     firewall_set_netfilter.cc
                     firewall_set_pf.cc
                     )
# install(TARGETS firewall LIBRARY DESTINATION ${XORP_LIBDIR})
set_target_properties(firewall PROPERTIES POSITION_INDEPENDENT_CODE TRUE)
