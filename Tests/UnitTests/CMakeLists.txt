if(NOT((WIN32 AND NOT WINDOWS_STORE) OR (APPLE AND NOT IOS) OR (UNIX AND NOT ANDROID)))
    message(FATAL_ERROR "Unsupported platform: ${CMAKE_SYSTEM_NAME}")
endif()

set(SCRIPTS
    "index.js")

set(EXTERNAL_SCRIPTS
    "../node_modules/chai/chai.js"
    "../node_modules/mocha/mocha.js")

set(SOURCES
    "App.cpp")

if(APPLE)
    find_library(JAVASCRIPTCORE_LIBRARY JavaScriptCore)
    set(ADDITIONAL_LIBRARIES ${JAVASCRIPTCORE_LIBRARY})
endif()

add_executable(UnitTests ${SOURCES} ${SCRIPTS} ${EXTERNAL_SCRIPTS})

target_link_libraries(UnitTests
    AppRuntime
    Console
    Scheduling
    ScriptLoader
    UrlLib
    XMLHttpRequest
    ${ADDITIONAL_LIBRARIES})

add_test(NAME UnitTests COMMAND UnitTests)

foreach(SCRIPT ${SCRIPTS} ${EXTERNAL_SCRIPTS})
    get_filename_component(SCRIPT_NAME "${SCRIPT}" NAME)
    add_custom_command(
        OUTPUT "${CMAKE_CFG_INTDIR}/Scripts/${SCRIPT_NAME}"
        COMMAND "${CMAKE_COMMAND}" -E copy "${CMAKE_CURRENT_SOURCE_DIR}/${SCRIPT}" "${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_CFG_INTDIR}/Scripts/${SCRIPT_NAME}"
        COMMENT "Copying ${SCRIPT_NAME}"
        MAIN_DEPENDENCY "${CMAKE_CURRENT_SOURCE_DIR}/${SCRIPT}")
endforeach()

set_property(TARGET UnitTests PROPERTY FOLDER Tests)
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${SOURCES} ${SCRIPTS})
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/../node_modules PREFIX node_modules FILES ${EXTERNAL_SCRIPTS})
