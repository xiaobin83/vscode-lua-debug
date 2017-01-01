cmake_minimum_required(VERSION 2.8)

project(vscode-lua-debug)

# find lua
find_path(LUA_INCLUDE_DIRS lua.hpp)
find_library(LUA_LIBRARY_DIR liblua.lib)

# asmjit
find_path(ASMJIT_INCLUDE_DIRS asmjit/asmjit.h)
find_library(ASMJIT_LIBRARY_DIR asmjit.lib)

# src
file(GLOB SRCS src/dbg_*.cpp src/dbg_*.h src/debugger.cpp src/debugger.h)
file(GLOB CLIENT_SRCS client/*.cpp client/*.h)
include_directories(
	${LUA_INCLUDE_DIRS} 
	${ASMJIT_INCLUDE_DIRS}
	${CMAKE_SOURCE_DIR}/src)

add_definitions(
	-DDEBUGGER_DELAYLOAD_LUA 
	-DRAPIDJSON_HAS_STDSTRING 
	-D_CRT_SECURE_NO_WARNINGS
	-D_WINSOCK_DEPRECATED_NO_WARNINGS)





# vscode-debug
set(BUILD_SHARED_LIBS ON)
add_library(vscode-debug ${SRCS})
target_compile_definitions(vscode-debug PRIVATE DEBUGGER_EXPORTS)
target_link_libraries(vscode-debug ${LUA_LIBRARY_DIR}/liblua.lib ${ASMJIT_LIBRARY_DIR}/asmjit.lib Ws2_32.lib)

# client
add_executable(vscode-debug-client ${CLIENT_SRCS})
target_link_libraries(vscode-debug-client vscode-debug ${LUA_LIBRARY_DIR}/liblua.lib Ws2_32.lib)



