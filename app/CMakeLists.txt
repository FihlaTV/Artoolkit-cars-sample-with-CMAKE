cmake_minimum_required(VERSION 3.4.1)

set(ARTOOLKIT_ROOT $ENV{ARTOOLKIT_ROOT})
set(NDK_ROOT $ENV{NDK_ROOT})

include_directories(src/main/jni)
include_directories(${ARTOOLKIT_ROOT}/include)
include_directories(${ARTOOLKIT_ROOT}/include/android)

add_library(arWrapper SHARED IMPORTED)
set_target_properties(arWrapper PROPERTIES
            #IMPORTED_LOCATION main/libs/${ANDROID_ABI}/libARWrapper.so)
            IMPORTED_LOCATION ${ARTOOLKIT_ROOT}/android/obj/local/${ANDROID_ABI}/libARWrapper.so)

# add static libraries
set(STATIC_LIBS
                ar2
                kpm
                util
                argsub_es
                armulti
                ar
                aricp
                jpeg
                arvideo
                eden
                )

foreach(LIB ${STATIC_LIBS})
    message("add lib: ${LIB}")
    add_library(${LIB} STATIC IMPORTED)
    set_target_properties(${LIB} PROPERTIES
            IMPORTED_LOCATION ${ARTOOLKIT_ROOT}/android/obj/local/${ANDROID_ABI}/lib${LIB}.a)
endforeach(LIB)

add_library(
             ARWrapperNativeCarsExample
             SHARED
             src/main/cpp/ARWrapperNativeCarsExample.cpp
             )

find_library( # Sets the name of the path variable.
              local-libs

              # Specifies the name of the NDK library that
              # you want CMake to locate.
              log
			  #android
			  )

target_link_libraries( # Specifies the target library.
                        ARWrapperNativeCarsExample

                       # Links the target library to the log library
                       # included in the NDK.
                       arWrapper
                       EGL
                       GLESv1_CM
                       ${STATIC_LIBS}
                       ${local-libs}
					   )