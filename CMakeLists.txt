cmake_minimum_required(VERSION 2.8)

if(NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  if(DEFINED ENV{VITASDK})
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VITASDK}/share/vita.toolchain.cmake" CACHE PATH "toolchain file")
  else()
    message(FATAL_ERROR "Please define VITASDK to point to your SDK path!")
  endif()
endif()

project(notahero C CXX)
include("${VITASDK}/share/vita.cmake" REQUIRED)
set(VITA_APP_NAME "NOT A HERO")
set(VITA_TITLEID  "NAHR00001")
set(VITA_MKSFOEX_FLAGS "-d ATTRIBUTE2=12")

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -g -Wl,-q,--wrap,memcpy,--wrap,memset,--allow-multiple-definition -D_GNU_SOURCE -Wall -O3 -fdiagnostics-color=always -fno-optimize-sibling-calls -mfloat-abi=softfp")
set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} -std=c++11 -Wno-write-strings -fpermissive -fno-rtti")

add_executable(notahero
  loader/main.c
  loader/dialog.c
  loader/so_util.c
  loader/sha1.c
  loader/trophies.c
  loader/ctype_patch.c
)

target_link_libraries(notahero
  -Wl,--whole-archive pthread -Wl,--no-whole-archive
  SDL2_mixer
  SDL2_image
  SDL2_ttf
  SDL2_net
  freetype
  soloud_static
  SDL2
  png
  webp
  jpeg
  mikmod
  z
  m
  stdc++
  opensles
  sndfile
  vorbis
  vorbisenc
  vorbisfile
  ogg
  FLAC
  openal
  imgui
  vitaGL
  vitashark
  SceShaccCgExt
  mathneon
  mpg123
  taihen_stub
  kubridge_stub
  SceNet_stub
  SceNetCtl_stub
  SceAppMgr_stub
  SceAppUtil_stub
  SceAudio_stub
  SceAudioIn_stub
  SceCtrl_stub
  SceCommonDialog_stub
  SceDisplay_stub
  SceFios2_stub
  SceGxm_stub
  SceIme_stub
  SceLibcBridge_stub
  SceShaccCg_stub
  SceSysmodule_stub
  ScePower_stub
  SceTouch_stub
  SceMotion_stub
  SceNpTrophy_stub
  SceHid_stub
  SceVshBridge_stub
  SceKernelDmacmgr_stub
)

vita_create_self(eboot.bin notahero UNSAFE)
vita_create_vpk(notahero.vpk ${VITA_TITLEID} eboot.bin
  VERSION ${VITA_VERSION}
  NAME ${VITA_APP_NAME}
  FILE ${CMAKE_SOURCE_DIR}/livearea/icon0.png sce_sys/icon0.png
       ${CMAKE_SOURCE_DIR}/livearea/TROPHY.TRP sce_sys/trophy/NAHR00001_00/TROPHY.TRP
       ${CMAKE_SOURCE_DIR}/livearea/bg0.png sce_sys/livearea/contents/bg0.png
       ${CMAKE_SOURCE_DIR}/livearea/template.xml sce_sys/livearea/contents/template.xml
)
