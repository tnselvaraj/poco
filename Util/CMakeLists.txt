# Sources
file(GLOB SRCS_G "src/*.cpp")
POCO_SOURCES_AUTO( SRCS ${SRCS_G})

# Headers
file(GLOB_RECURSE HDRS_G "include/*.h" )
POCO_HEADERS_AUTO( SRCS ${HDRS_G})

POCO_SOURCES_AUTO_PLAT( SRCS WIN32
    src/WinRegistryConfiguration.cpp
    src/WinRegistryKey.cpp
    src/WinService.cpp
)

add_library(Util ${SRCS} )
add_library(Poco::Util ALIAS Util)
set_target_properties(Util
    PROPERTIES
    VERSION ${SHARED_LIBRARY_VERSION} SOVERSION ${SHARED_LIBRARY_VERSION}
	OUTPUT_NAME PocoUtil
    DEFINE_SYMBOL Util_EXPORTS
    )

target_link_libraries(Util PUBLIC Poco::Foundation)
if (POCO_ENABLE_XML)
	target_link_libraries(Util PUBLIC Poco::XML)
else ()
	target_compile_definitions(Util PUBLIC POCO_UTIL_NO_XMLCONFIGURATION )
endif()
if (POCO_ENABLE_JSON)
	target_link_libraries(Util PUBLIC Poco::JSON)
else ()
	target_compile_definitions(Util PUBLIC POCO_UTIL_NO_JSONCONFIGURATION )
endif()

target_include_directories(Util
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
    PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src
    )

POCO_INSTALL(Util)
POCO_GENERATE_PACKAGE(Util)

if (POCO_ENABLE_TESTS)
    add_subdirectory(testsuite)
endif ()
if (POCO_ENABLE_SAMPLES)
    add_subdirectory(samples)
endif ()

configure_file(PocoUtil.pc.in ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/pkgconfig/PocoUtil.pc @ONLY)
list(APPEND POCO_PKG_CONFIG_FILES ${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/pkgconfig/PocoUtil.pc)
set(POCO_PKG_CONFIG_FILES ${POCO_PKG_CONFIG_FILES} PARENT_SCOPE)
