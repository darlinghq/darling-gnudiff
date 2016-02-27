project(gnudiff)

cmake_minimum_required(VERSION 2.4.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
	cmake_policy(SET CMP0005 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_definitions(-DTARGET_OS_MAC=1)
add_definitions(-D__APPLE__ -D__DYNAMIC__)
add_definitions(-DHAVE_CONFIG_H)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -U_POSIX_C_SOURCE -D__DARWIN_UNIX03 -nostdinc -fPIC")
set(CMAKE_EXE_LINKER_FLAGS "-nodefaultlibs -nostdlib -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")

include_directories(${DARLING_TOP_DIRECTORY}/src/libc/include/FreeBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/include/NetBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/gen)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/darwin)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/stdtime/FreeBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/launchd/liblaunch)
include_directories(${DARLING_TOP_DIRECTORY}/src/external/libdispatch)
include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/diffutils/lib)
include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/diffutils/lib/posix)
include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/diffutils)

set(diffutils_sources
	diffutils/lib/basename.c
	diffutils/lib/cmpbuf.c
	diffutils/lib/c-stack.c
	diffutils/lib/error.c
	diffutils/lib/exclude.c
	diffutils/lib/exitfail.c
	diffutils/lib/fnmatch.c
	diffutils/lib/freesoft.c
	diffutils/lib/getopt1.c
	diffutils/lib/getopt.c
	diffutils/lib/hard-locale.c
	diffutils/lib/imaxtostr.c
	diffutils/lib/offtostr.c
	diffutils/lib/posixver.c
	diffutils/lib/prepargs.c
	diffutils/lib/quotesys.c
	diffutils/lib/regex.c
	diffutils/lib/setmode.c
	diffutils/lib/strftime.c
	diffutils/lib/umaxtostr.c
	diffutils/lib/xmalloc.c
	diffutils/lib/xstrtoumax.c
	diffutils/src/version.c
)
add_library(diffutils OBJECT ${diffutils_sources})

set(diff_sources
    diffutils/src/analyze.c
    diffutils/src/context.c
    diffutils/src/diff.c
    diffutils/src/dir.c
    diffutils/src/ed.c
    diffutils/src/ifdef.c
    diffutils/src/io.c
    diffutils/src/normal.c
    diffutils/src/side.c
    diffutils/src/util.c
)

add_darling_executable(cmp $<TARGET_OBJECTS:diffutils> diffutils/src/cmp.c)
add_darling_executable(diff $<TARGET_OBJECTS:diffutils> ${diff_sources})
add_darling_executable(diff3 $<TARGET_OBJECTS:diffutils> diffutils/src/diff3.c)
add_darling_executable(sdiff $<TARGET_OBJECTS:diffutils> diffutils/src/sdiff.c)

install(TARGETS diff cmp diff3 sdiff
	DESTINATION libexec/darling/usr/bin)

