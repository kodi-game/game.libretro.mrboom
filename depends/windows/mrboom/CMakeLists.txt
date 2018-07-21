cmake_minimum_required(VERSION 3.1)
project(mrboom)

list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR})

include(ExternalProject)

string(TOUPPER ${PROJECT_NAME} PROJECT_NAME_UC)

set(LIBRETRO_BINARY_DIR .)
set(LIBRETRO_SONAME mrboom_libretro${CMAKE_SHARED_LIBRARY_SUFFIX})
set(LIBRETRO_JNISONAME libretro${CMAKE_SHARED_LIBRARY_SUFFIX})

add_library(${PROJECT_NAME}_libretro SHARED
  ai/Bot.cpp
  ai/BotTree.cpp
  ai/GridFunctions.cpp
  ai/MrboomHelper.cpp
  common.cpp
  libretro-common/compat/compat_posix_string.c
  libretro-common/compat/compat_strcasestr.c
  libretro-common/compat/compat_strl.c
  libretro-common/encodings/encoding_utf.c
  libretro-common/file/file_path.c
  libretro-common/lists/string_list.c
  libretro-common/memmap/memalign.c
  libretro-common/streams/file_stream.c
  libretro-common/streams/memory_stream.c
  libretro-common/string/stdstring.c
  mrboom.c
  retro.cpp
)
target_include_directories(${PROJECT_NAME}_libretro PRIVATE . ai PUBLIC libretro-common/include)
target_compile_definitions(${PROJECT_NAME}_libretro PRIVATE -DMRBOOM -D_FORTIFY_SOURCE=0 -D__LIBRETRO__ -Dstrcasecmp=_stricmp)
install(TARGETS ${PROJECT_NAME}_libretro EXPORT ${PROJECT_NAME}_libretro RUNTIME DESTINATION lib ARCHIVE DESTINATION lib)
if(MSVC)
  install(FILES $<TARGET_PDB_FILE:${PROJECT_NAME}_libretro> DESTINATION lib CONFIGURATIONS Debug RelWithDebInfo)
endif()

# write the config.cmake script
file(WRITE ${CMAKE_INSTALL_PREFIX}/libretro-${PROJECT_NAME}-config.cmake "set(${PROJECT_NAME_UC}_LIB ${CMAKE_INSTALL_PREFIX}/lib/${LIBRETRO_SONAME})")
