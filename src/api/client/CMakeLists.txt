set(cxx-sources
  Issuer.cpp
  Pair.cpp
  ServerAction.cpp
  Sync.cpp
  Wallet.cpp
)

file(GLOB cxx-install-headers
     "${CMAKE_CURRENT_SOURCE_DIR}/../../../include/opentxs/api/client/*.hpp"
)

set(cxx-headers
  ${cxx-install-headers}
  ${CMAKE_CURRENT_SOURCE_DIR}/../../../include/opentxs/api/client/implementation/Pair.hpp
  ${CMAKE_CURRENT_SOURCE_DIR}/../../../include/opentxs/api/client/implementation/ServerAction.hpp
  ${CMAKE_CURRENT_SOURCE_DIR}/../../../include/opentxs/api/client/implementation/Wallet.hpp
  ${CMAKE_CURRENT_SOURCE_DIR}/Issuer.hpp
  ${CMAKE_CURRENT_SOURCE_DIR}/Sync.hpp
)

set(MODULE_NAME opentxs-api-client)

if(WIN32)
  # suppress warnings about exported internal symbols (global log stream objects)
  add_definitions(-DOTLOG_IMPORT)

  # suppress linker warnings about locallly defined imported symbols
  set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} /ignore:4049 /ignore:4217")

  configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/../../cmake/windll.rc.in
    ${CMAKE_CURRENT_BINARY_DIR}/module.rc
    @ONLY
  )

  # we have an extra Windows-specific file to compile
  add_library(${MODULE_NAME} OBJECT
    ${cxx-sources}
    ${cxx-headers}
    util/OTWindowsRegistryTools.cpp
    ${CMAKE_CURRENT_BINARY_DIR}/module.rc
  )
else()
  add_library(${MODULE_NAME} OBJECT
    ${cxx-sources}
    ${cxx-headers}
  )
endif()

install(FILES ${cxx-install-headers}
        DESTINATION include/opentxs/api/client
        COMPONENT dev)

set_property(TARGET ${MODULE_NAME} PROPERTY POSITION_INDEPENDENT_CODE 1)
set_lib_property(${MODULE_NAME})
