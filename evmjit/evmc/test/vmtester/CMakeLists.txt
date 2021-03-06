# EVMC: Ethereum Client-VM Connector API.
# Copyright 2018 Pawel Bylica.
# Licensed under the MIT License. See the LICENSE file.

include(GNUInstallDirs)

hunter_add_package(Boost COMPONENTS program_options filesystem system)
find_package(Boost CONFIG REQUIRED program_options filesystem system)

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)

add_executable(evmc-vmtester vmtester.hpp vmtester.cpp tests.cpp)

target_link_libraries(
    evmc-vmtester
    PRIVATE
    evmc
    GTest::gtest
    Boost::program_options
    Boost::filesystem
    Boost::system
    ${CMAKE_DL_LIBS}
)

install(TARGETS evmc-vmtester RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

add_test(NAME vmtester-help
    COMMAND evmc-vmtester --help
)
set_tests_properties(vmtester-help PROPERTIES
    PASS_REGULAR_EXPRESSION "Options:"
)

add_test(NAME vmtester-nonexistingvm
    COMMAND evmc-vmtester nonexistingvm
)
set_tests_properties(vmtester-nonexistingvm PROPERTIES
    WILL_FAIL TRUE
    FAIL_REGULAR_EXPRESSION "no such file"
)

add_test(NAME vmtester-novm
    COMMAND evmc-vmtester
)
set_tests_properties(vmtester-novm PROPERTIES
    WILL_FAIL TRUE
    FAIL_REGULAR_EXPRESSION "required but missing"
)
