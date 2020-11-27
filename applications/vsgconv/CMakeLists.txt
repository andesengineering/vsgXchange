if(NOT ANDROID)
    find_package(Threads)
endif()

if (UNIX)
    find_library(DL_LIBRARY dl)
endif()

set(SOURCES
    vsgconv.cpp
)

add_executable(vsgconv ${SOURCES})

target_include_directories(vsgconv PRIVATE
    $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include>
)

set_target_properties(vsgconv PROPERTIES OUTPUT_NAME vsgconv)

target_link_libraries(vsgconv
    vsgXchange
    vsg::vsg
)

install(TARGETS vsgconv
        RUNTIME DESTINATION bin
)

if(WIN32)
	#copy vsg dlls into target directories
	get_target_property(VSG_LOCATION_DEBUG vsg::vsg IMPORTED_LOCATION_DEBUG)
	get_target_property(VSG_LOCATION_RELEASE vsg::vsg IMPORTED_LOCATION_RELEASE)
	add_custom_command(TARGET vsgconv POST_BUILD
				   COMMAND ${CMAKE_COMMAND} -E copy_if_different 
				   $<$<CONFIG:Debug>:${VSG_LOCATION_DEBUG}> 
				   $<$<NOT:$<CONFIG:Debug>>:${VSG_LOCATION_RELEASE}> 
				   $<TARGET_FILE_DIR:vsgconv>)
				   
	install(DIRECTORY ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/$<CONFIG>/ DESTINATION bin FILES_MATCHING PATTERN "*.dll")
endif(WIN32)