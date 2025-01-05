cmake_minimum_required(VERSION 3.26)

# CREATE PROJECT
project(imnodeflow)

set(CMAKE_DEBUG_POSTFIX d)
if(APPLE)
    set(CMAKE_CXX_STANDARD 11)
    enable_language(OBJCXX)
endif()

add_library(${PROJECT_NAME} "")
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

add_compile_definitions(IMGUI_DEFINE_MATH_OPERATORS)

# SET SOURCE FILES FOR PROJECT
file(GLOB_RECURSE _HDRS "include/*.h")
file(GLOB_RECURSE _HDRS2 "src/*.h" "src/*.inl")
file(GLOB_RECURSE _SRCS "src/*.cpp" "src/*.h" "src/*.inl")

target_include_directories(${PROJECT_NAME} PUBLIC
		$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
		$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

target_sources(
    ${PROJECT_NAME}
    PRIVATE
    ${_SRCS}
)

# FIND DEPENDENCIES
find_package(imgui)

# LINK CONAN LIBS
target_link_libraries(${PROJECT_NAME} imgui::imgui)

# VCPKG
install(FILES
	${_HDRS}
	DESTINATION include/imnodeflow/include/
	)
    
install(FILES
	${_HDRS2}
	DESTINATION include/imnodeflow/src/
	)

list(REMOVE_DUPLICATES BINDINGS_SOURCES)

install(
    TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}_target
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

foreach(BINDING_TARGET ${BINDING_TARGETS})
    install(
        TARGETS ${BINDING_TARGET}
        EXPORT ${PROJECT_NAME}_target
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        RUNTIME DESTINATION bin
    )
endforeach()

install(
    EXPORT ${PROJECT_NAME}_target
    NAMESPACE ${PROJECT_NAME}::
    FILE ${PROJECT_NAME}-targets.cmake
    DESTINATION share/${PROJECT_NAME}
)
