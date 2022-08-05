cmake_minimum_required(VERSION 3.1)
project(MiSTerRTGDriver VERSION 0.2 LANGUAGES ASM)

string(TIMESTAMP TODAY "%Y-%m-%d")
configure_file(Version.i.in Version.i)

set(TARGET MiSTer.card)

set(CMAKE_ASM_COMPILER /opt/amiga/bin/vasmm68k_mot)
set(CMAKE_LINKER /opt/amiga/bin/vlink)

set(CMAKE_ASM_FLAGS "-quiet -Iinclude -Fhunk -opt-fconst")
set(CMAKE_ASM_FLAGS_DEBUG "${CMAKE_ASM_FLAGS} -Ddebug")
set(CMAKE_ASM_FLAGS_RELEASE "${CMAKE_ASM_FLAGS} -nosym")

set(CMAKE_LINKER_FLAGS "-b amigahunk -Bstatic -nostdlib")
set(CMAKE_LINKER_FLAGS_DEBUG "${CMAKE_LINKER_FLAGS}")
set(CMAKE_LINKER_FLAGS_RELEASE "${CMAKE_LINKER_FLAGS} -S -s")

set(SOURCES
  MiSTer.card.asm 
)

# If you define the Debug Symbol make sure the monitor file is in
# sys:storage/monitors - debug output seems to crash the system if
# it happens during startup.
if (CMAKE_BUILD_TYPE STREQUAL "Debug")
  set(CMAKE_ASM_COMPILE_OBJECT "<CMAKE_ASM_COMPILER> ${CMAKE_ASM_FLAGS_DEBUG} -o <OBJECT> <SOURCE>")
  set(CMAKE_ASM_LINK_EXECUTABLE "${CMAKE_LINKER} ${CMAKE_EXE_LINKER_FLAGS_DEBUG} <OBJECTS> -o <TARGET> <LINK_LIBRARIES>")
else()
  set(CMAKE_ASM_COMPILE_OBJECT "<CMAKE_ASM_COMPILER> ${CMAKE_ASM_FLAGS_RELEASE} -o <OBJECT> <SOURCE>")
  set(CMAKE_ASM_LINK_EXECUTABLE "${CMAKE_LINKER} ${CMAKE_EXE_LINKER_FLAGS_RELEASE} <OBJECTS> -o <TARGET> <LINK_LIBRARIES>")
endif(CMAKE_BUILD_TYPE STREQUAL "Debug")

add_executable(${TARGET} ${SOURCES})

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/build)
