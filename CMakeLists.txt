cmake_minimum_required(VERSION 3.11)

project(bareiron)

include_directories(
	${CMAKE_SOURCE_DIR}/include
	${CMAKW_SOURCE_DIR}/src
)

add_executable(${PROJECT_NAME} src/main.c
	src/globals.c
	src/packets.c
	src/registries.c
	src/structures.c
	src/varnum.c
	src/crafting.c
	src/procedures.c
	src/serialize.c
	src/tools.c
	src/worldgen.c
	include/crafting.h
	include/packets.h
	include/registries.h
	include/structures.h
	include/varnum.h
	include/globals.h
	include/procedures.h
	include/serialize.h
	include/tools.h
	include/worldgen.h
)

target_link_libraries(${PROJECT_NAME} PRIVATE
    pspdebug
    pspdisplay
    pspge
    pspsdk
)

# Create an EBOOT.PBP file
create_pbp_file(
    TARGET ${PROJECT_NAME}
    ICON_PATH NULL
    BACKGROUND_PATH NULL
    PREVIEW_PATH NULL
    TITLE ${PROJECT_NAME}
    VERSION 01.00
)
