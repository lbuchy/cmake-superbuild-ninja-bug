cmake_minimum_required(VERSION 3.4)

add_library(foo src/foo.cpp)

target_include_directories(foo PUBLIC 
	$<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/include>
	$<INSTALL_INTERFACE:include>
	)

install(TARGETS foo DESTINATION lib/foo EXPORT foo-config)
install(EXPORT foo-config DESTINATION lib/foo)
install(FILES include/foo.h DESTINATION include)

#export(TARGETS foo FILE foo-exports.cmake)
