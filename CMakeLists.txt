cmake_minimum_required(VERSION 3.5)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-unused-value")

idf_component_register(SRCS ${SOURCES}
                    INCLUDE_DIRS "${CMAKE_CURRENT_LIST_DIR}/rlottie/inc"
                    )

set(LOTTIE_MODULE OFF)
set(LOTTIE_THREAD OFF)
set(BUILD_SHARED_LIBS OFF)
option(BUILD_TESTING OFF)

function(install)
endfunction()

function(export)
endfunction()

add_subdirectory(rlottie)
target_link_libraries(${COMPONENT_LIB} INTERFACE rlottie)

project(esp_rlottie)

if(ESP_PLATFORM)
    message(STATUS "Configuring for ESP platform")
    # Add your ESP-specific configuration here
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DESP_PLATFORM")
endif()