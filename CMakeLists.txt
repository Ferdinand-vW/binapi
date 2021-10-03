cmake_minimum_required(VERSION 3.2)
project(binapi)

set(CMAKE_CXX_STANDARD 14)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -fsanitize=address")

add_definitions(
    -UNDEBUG
    -DDTF_HEADER_ONLY
    -D__FLATJSON__CHILDS_TYPE=std::uint32_t
    -D__FLATJSON__VLEN_TYPE=std::uint32_t
)

include_directories(
    ./include
)

if (DEFINED ${BOOST_INCLUDE_DIR})
    include_directories(
        ${BOOST_INCLUDE_DIR}
    )
endif()

set(BINAPI_INCLUDE_DIRS ${CMAKE_CURRENT_LIST_DIR}/include)

set(BINAPI_HEADERS
    ${BINAPI_INCLUDE_DIRS}/binapi/api.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/flatjson.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/dtf.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/double_type.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/enums.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/errors.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/invoker.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/message.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/pairslist.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/reports.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/tools.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/types.hpp
    ${BINAPI_INCLUDE_DIRS}/binapi/websocket.hpp
)

set(BINAPI_SOURCES
    src/api.cpp
    src/enums.cpp
    src/errors.cpp
    src/pairslist.cpp
    src/reports.cpp
    src/tools.cpp
    src/types.cpp
    src/websocket.cpp
)

add_executable(
    ${PROJECT_NAME}
    #
    main.cpp
    #
    ${BINAPI_SOURCES}
)

set(LibName ${PROJECT_NAME}_lib)

add_library(
    ${LibName}
    ${BINAPI_SOURCES}
)

if (DEFINED ${BOOST_LIB_DIR})
    target_link_libraries(
        ${PROJECT_NAME}
        -L${BOOST_LIB_DIR}
    )

    target_link_libraries(
        ${LibName}
        -L${BOOST_LIB_DIR}
    )
endif()

target_link_libraries(
    ${PROJECT_NAME}
    z
    crypto
    ssl
    pthread
)

target_link_libraries(
    ${LibName}
    z
    crypto
    ssl
    pthread
)

target_include_directories(${LibName}
    PUBLIC
        ${BINAPI_HEADERS}
)