
# 3.1 is required for ZLIB import target support
cmake_minimum_required(VERSION 3.1)

# set the project name
project(qtiocompressor-qt5 VERSION 2.3.2)

# specify the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Set QTIOCOMPRESSOR_STATIC to TRUE to compile a static library
if (NOT DEFINED QTIOCOMPRESSOR_STATIC)
	set(QTIOCOMPRESSOR_STATIC FALSE)
endif()

# Set QTIOCOMPRESSOR_BUILD_EXAMPLES to FALSE to avoid building the example
if (NOT DEFINED QTIOCOMPRESSOR_BUILD_EXAMPLES)
	set(QTIOCOMPRESSOR_BUILD_EXAMPLES TRUE)
endif()


set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)

if(CMAKE_VERSION VERSION_LESS "3.7.0")
    set(CMAKE_INCLUDE_CURRENT_DIR ON)
endif()

find_package(Qt5 COMPONENTS Core REQUIRED)
find_package(ZLIB REQUIRED)


set(qtiocompressor_LIB_SOURCES
	src/QtIOCompressor
	src/qtiocompressor.cpp
	src/qtiocompressor.h
)


if (QTIOCOMPRESSOR_STATIC)
	# Static library
	add_library(qtiocompressor STATIC ${qtiocompressor_LIB_SOURCES})
	target_compile_definitions(qtiocompressor
		PUBLIC
			QT_QTIOCOMPRESSOR_LIBRARY_STATIC
	)
else()
	# Dynamic library
	add_library(qtiocompressor SHARED ${qtiocompressor_LIB_SOURCES})
	# Put the import library along with dll
	set_target_properties(qtiocompressor PROPERTIES ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")
endif()

# Handle visibility symbols
target_compile_definitions(qtiocompressor
	PRIVATE
		QT_QTIOCOMPRESSOR_LIBRARY_BUILD
)


target_link_libraries(qtiocompressor
	PUBLIC  # These are part of the API
		Qt5::Core
	PRIVATE
		ZLIB::ZLIB
)

target_include_directories(qtiocompressor
	SYSTEM INTERFACE
		${CMAKE_CURRENT_SOURCE_DIR}/src
)



# Enable warnings
if (MSVC)
	# warning level 4
	target_compile_options(qtiocompressor
		PRIVATE
			/W4
	)
else()
	# warning level 4
	target_compile_options(qtiocompressor
		PRIVATE
			-Wall -Wextra
	)
endif()

# Disable unnecessary warnings
if (CMAKE_CXX_COMPILER_ID STREQUAL "GNU" OR CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
	target_compile_options(qtiocompressor
		PRIVATE
			-Wno-shadow
	)
	if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
		target_compile_options(qtiocompressor
			PRIVATE
				-Wno-shorten-64-to-32
		)
	endif()
endif()



# --- Examples

set(qtiocompressor_EXAMPLE_GZIP_SOURCES
	examples/gzip/main.cpp
)

set(qtiocompressor_EXAMPLE_SIMPLE_SOURCES
	examples/simple/main.cpp
)

set(qtiocompressor_EXAMPLE_XML_SOURCES
	examples/xml/main.cpp
)

set(qtiocompressor_EXAMPLE_ZIPLIST_SOURCES
	examples/ziplist/main.cpp
)

if (QTIOCOMPRESSOR_BUILD_EXAMPLES)

	# The xml example needs more libraries
	find_package(Qt5 COMPONENTS Xml REQUIRED)

	# gzip
	add_executable(qtiocompressor_example_gzip ${qtiocompressor_EXAMPLE_GZIP_SOURCES})
	target_link_libraries(qtiocompressor_example_gzip PRIVATE qtiocompressor)
	if (MSVC)
		target_compile_options(qtiocompressor_example_gzip PRIVATE /W4)
	else()
		target_compile_options(qtiocompressor_example_gzip PRIVATE -Wall -Wextra)
	endif()

	# simple
	add_executable(qtiocompressor_example_simple ${qtiocompressor_EXAMPLE_SIMPLE_SOURCES})
	target_link_libraries(qtiocompressor_example_simple PRIVATE qtiocompressor)
	if (MSVC)
		target_compile_options(qtiocompressor_example_simple PRIVATE /W4)
	else()
		target_compile_options(qtiocompressor_example_simple PRIVATE -Wall -Wextra)
	endif()

	# xml
	add_executable(qtiocompressor_example_xml ${qtiocompressor_EXAMPLE_XML_SOURCES})
	target_link_libraries(qtiocompressor_example_xml PRIVATE qtiocompressor Qt5::Xml)
	if (MSVC)
		target_compile_options(qtiocompressor_example_xml PRIVATE /W4)
	else()
		target_compile_options(qtiocompressor_example_xml PRIVATE -Wall -Wextra)
	endif()

	# ziplist
	add_executable(qtiocompressor_example_ziplist ${qtiocompressor_EXAMPLE_ZIPLIST_SOURCES})
	target_link_libraries(qtiocompressor_example_ziplist PRIVATE qtiocompressor)
	if (MSVC)
		target_compile_options(qtiocompressor_example_ziplist PRIVATE /W4)
	else()
		target_compile_options(qtiocompressor_example_ziplist PRIVATE -Wall -Wextra)
	endif()

endif()
