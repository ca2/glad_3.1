cmake_minimum_required(VERSION 3.16)
project(glad CXX)


#list(APPEND library_references
 #  acme
  # )


list(APPEND library_source
   glad.c
   )


if(WIN32)

    #list(APPEND audio_source
       #${PROJECT_NAME}/windows/tts_speaker.cpp
       #${PROJECT_NAME}/windows/tts_speaker.h
     #  )

elseif(${LINUX} OR ${__BSD__})


    list(APPEND library_source
       glad_egl.c
         )

endif()


add_library(${PROJECT_NAME} SHARED ${library_source})
add_library(static_${PROJECT_NAME} STATIC ${library_source})


#target_precompile_headers(${PROJECT_NAME} PRIVATE framework.h)
#target_precompile_headers(static_${PROJECT_NAME} PRIVATE framework.h)


set_target_properties(${PROJECT_NAME} PROPERTIES LINKER_LANGUAGE CXX)

target_compile_definitions(${PROJECT_NAME} PRIVATE HAVE_CONFIG_H)
target_compile_definitions(static_${PROJECT_NAME} PRIVATE HAVE_CONFIG_H)


if (MSVC)
    target_compile_options(${PROJECT_NAME} PRIVATE "/MD$<$<CONFIG:Debug>:d>")
    target_link_options(${PROJECT_NAME} PRIVATE "/DEFAULTLIB:msvcrt$<$<CONFIG:Debug>:d>")
    target_link_options(${PROJECT_NAME} PRIVATE "/SUBSYSTEM:WINDOWS")
    target_link_libraries(${PROJECT_NAME} PRIVATE ${library_references})
    target_link_libraries(${PROJECT_NAME} PRIVATE ws2_32 winmm shlwapi)
elseif(LINUX OR FREEBSD)


    #target_compile_options(${PROJECT_NAME} PRIVATE $<$<COMPILE_LANGUAGE:CXX>:-fpermissive  ${EXTRA_CXX_TARGET_COMPILER_OPTIONS}>)
    #target_compile_options(static_${PROJECT_NAME} PRIVATE $<$<COMPILE_LANGUAGE:CXX>:-fpermissive  ${EXTRA_CXX_TARGET_COMPILER_OPTIONS}>)
    target_compile_definitions(static_${PROJECT_NAME} PRIVATE CUBE)


    #target_link_options(${PROJECT_NAME} PRIVATE -Wl,-rpath=$ORIGIN)
    target_link_libraries(${PROJECT_NAME} PRIVATE pthread z EGL GL)
    target_link_libraries(${PROJECT_NAME} PRIVATE ${library_references})
    target_link_options(${PROJECT_NAME} PRIVATE -Wl,-z,defs)
    target_link_options(static_${PROJECT_NAME} PRIVATE --whole-archive)


endif()




target_include_directories(${PROJECT_NAME} PRIVATE .)
target_include_directories(${PROJECT_NAME} PRIVATE $ENV{HOME}/sensitive/sensitive/include)
target_include_directories(${PROJECT_NAME} PRIVATE $ENV{__sensitive}/sensitive/include)
list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR}/../..)
list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR}/..)
list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR})
list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR}/include)
list(APPEND library_include_directories ${CMAKE_CURRENT_SOURCE_DIR}/../../_/${PROJECT_NAME})



target_include_directories(${PROJECT_NAME} PRIVATE ${library_include_directories})
target_include_directories(static_${PROJECT_NAME} PRIVATE ${library_include_directories})


target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/src)


target_compile_definitions(${PROJECT_NAME} PRIVATE _ID3_LIBRARY)



