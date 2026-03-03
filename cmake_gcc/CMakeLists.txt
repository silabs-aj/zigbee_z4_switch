# Define minimal required version of CMake.
cmake_minimum_required(VERSION "3.25")

# Project definition
project(
	zigbee_z4_switch
	VERSION 1.0
	LANGUAGES C CXX ASM
)

# Include the definition of the slc_zigbee_z4_switch target,
# which contains the content of the SLC project
include(zigbee_z4_switch.cmake)

add_executable(zigbee_z4_switch
    # Add additional sources here
)

target_include_directories(zigbee_z4_switch PUBLIC
    # Add additional include paths here
)

target_compile_definitions(zigbee_z4_switch PUBLIC
    # Add additional macros here
)

target_compile_options(zigbee_z4_switch PUBLIC
    # Set additional compiler flags here
)

target_link_options(zigbee_z4_switch PUBLIC
    # Set additional linker flags here
)

# Link with the content defined in the SLC project
target_link_libraries(zigbee_z4_switch PRIVATE
    slc
)

# Include managed project content if available
include(zigbee_z4_switch_project.cmake OPTIONAL RESULT_VARIABLE managed_project)
if(managed_project)
    message(STATUS "Using managed project content from ${managed_project}")
endif()

# Force the gcc linker command
set_target_properties(zigbee_z4_switch PROPERTIES LINKER_LANGUAGE C)

# Create .bin, .hex and .s37 artifacts after building the project
add_custom_command(TARGET zigbee_z4_switch
    POST_BUILD
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_SREC_CMD} "$<TARGET_FILE:zigbee_z4_switch>" "$<TARGET_FILE_DIR:zigbee_z4_switch>/$<TARGET_FILE_BASE_NAME:zigbee_z4_switch>.s37"
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_IHEX_CMD} "$<TARGET_FILE:zigbee_z4_switch>" "$<TARGET_FILE_DIR:zigbee_z4_switch>/$<TARGET_FILE_BASE_NAME:zigbee_z4_switch>.hex"
    COMMAND ${CMAKE_OBJCOPY} ${OBJCOPY_BIN_CMD}  "$<TARGET_FILE:zigbee_z4_switch>" "$<TARGET_FILE_DIR:zigbee_z4_switch>/$<TARGET_FILE_BASE_NAME:zigbee_z4_switch>.bin" 
)

# Run post-build pipeline to perform additional post-processing
if(post_build_command)
add_custom_command(TARGET zigbee_z4_switch
    POST_BUILD
    WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/..
    COMMAND ${post_build_command}
)
endif()