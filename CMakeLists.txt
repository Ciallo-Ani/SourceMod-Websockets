cmake_minimum_required(VERSION 3.5)
project(websocket)

set(SOURCE_FILES
    src/smsdk_ext.cpp
    src/extension.cpp
    src/event_loop.cpp
    src/websocket_connection_base.cpp
    src/websocket_connection_ssl.cpp
    src/websocket_connection.cpp
    src/json_natives.cpp
    src/smn_websocket.cpp
    CxxUrl/url.cpp)
    
# if(NOT TARGET)
#     set(TARGET Linux)
# endif()

if(${TARGET} STREQUAL Windows)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /std:c++17 /WIN32 /MT /W3 /GR- /DSOURCEMOD_BUILD")
    add_definitions(-DWIN32)
else()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -L${JANSSON_LIBRARYDIR} -std=c++17 -pipe -DSOURCEMOD_BUILD -D_LINUX -fno-strict-aliasing -m32")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -s -O3")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -g3")
endif()

set(CMAKE_SKIP_BUILD_RPATH true)
set(CMAKE_VERBOSE_MAKEFILE false)

if(${TARGET} STREQUAL Windows)
    include_directories(sourcemod/public sourcepawn/include amtl amtl/amtl jansson/build/include CxxUrl)
else()
    include_directories(sourcemod/public sourcepawn/include amtl amtl/amtl jansson/build/include CxxUrl)
endif()

add_library(websocket.ext SHARED ${SOURCE_FILES})

if(${TARGET} STREQUAL Windows)
    target_link_libraries(websocket.ext)
else()
    set_target_properties(websocket.ext PROPERTIES COMPILE_FLAGS "-m32" LINK_FLAGS "-m32")
    target_link_libraries(websocket.ext -Wl,--no-undefined -Wl,-Bstatic -lssl -lcrypto -ljansson -static-libstdc++ -static-libgcc -Wl,-Bdynamic -lpthread -ldl)
endif()

set_target_properties(websocket.ext PROPERTIES PREFIX "")

if(${TARGET} STREQUAL Windows)
    set_target_properties(websocket.ext PROPERTIES SUFFIX ".dll")
endif()
