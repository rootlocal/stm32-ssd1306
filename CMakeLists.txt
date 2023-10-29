set(LIB_NAME ssd1306_lib)

message(STATUS "Build: ${LIB_NAME}")

include_directories("src/inc" "src/sample/inc")
file(GLOB_RECURSE LIB_SOURCES "src/*.c")

ADD_LIBRARY(${LIB_NAME} STATIC ${LIB_SOURCES})