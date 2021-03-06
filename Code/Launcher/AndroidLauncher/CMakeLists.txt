#START-FILE-LIST
# File List auto generated by waf2cmake.py, do not modify manually.

set( SOURCE_FILES
	AndroidManifest.xml
	Main.cpp
)
source_group("Source Files" FILES ${SOURCE_FILES})

set( SOURCE_FILES_C
	../../SDKs/SDL2/src/src/main/android/SDL_android_main.c
)
source_group("C Source Files" FILES ${SOURCE_FILES_C})

set( SOURCE_FILES_JAVA
	SDLActivity.java
	CryEngineActivity.java
)
source_group("Java Files" FILES ${SOURCE_FILES_JAVA})

if(ANDROID)
	set( SOURCE_FILES_SDLEXT
		${CMAKE_SOURCE_DIR}/Code/Tools/SDLExtension/src/jni/SDLExt_android.c
		${CMAKE_SOURCE_DIR}/Code/Tools/SDLExtension/src/jni/SDLExt_android_init.c
	)
	source_group("SDL Extension" FILES ${SOURCE_FILES_SDLEXT})

	set( CFilesForAndroidLauncher_SOURCES
		${SOURCE_FILES_C}
		${SOURCE_FILES_SDLEXT}
	)
	# All .c files will be compiled by a dedicated static library because CMake with Nvidia Nsight does not support mixing C/C++ in one project
	#CryEngineStaticModule(CFilesForAndroidLauncher SOLUTION_FOLDER "Launcher")
	#target_link_libraries(${THIS_PROJECT} PRIVATE SDL2 SDLExt)
	add_library(CFilesForAndroidLauncher OBJECT ${CFilesForAndroidLauncher_SOURCES})
	
	set( AndroidLauncher_SOURCES
		${SOURCE_FILES}
		${SOURCE_FILES_JAVA}
	)

	set_source_files_properties(${SOURCE_FILES_SDLEXT} PROPERTIES HEADER_FILE_ONLY true)
	target_include_directories(CFilesForAndroidLauncher PRIVATE "${SDK_DIR}/SDL2/include/android-${CMAKE_ANDROID_ARCH}" "${CRYENGINE_DIR}/Code/Tools/SDLExtension/src/include")
	target_compile_definitions(CFilesForAndroidLauncher PRIVATE USE_SDL2)
	
	#CryLauncher(AndroidLauncher SOLUTION_FOLDER Launcher)
	
	set(THIS_PROJECT AndroidLauncher)
	project(AndroidLauncher)
	add_library(AndroidLauncher SHARED ${AndroidLauncher_SOURCES} $<TARGET_OBJECTS:CFilesForAndroidLauncher>)
	set_solution_folder("Launcher" AndroidLauncher)
	SET_PLATFORM_TARGET_PROPERTIES(AndroidLauncher)

	configure_android_build(APP_NAME "CRYENGINE SDK" PACKAGE com.crytek.cryengine PERMISSIONS WRITE_EXTERNAL_STORAGE READ_EXTERNAL_STORAGE INTERNET DEBUGGABLE VERSION_CODE 1 VERSION_NAME 1.0)
	configure_android_launcher(AndroidLauncher)

	target_compile_definitions(${THIS_PROJECT} PRIVATE 
		ANDROID_NDK 
		ANDROID 
		DISABLE_IMPORTGL 
		__ANDROID__ 
		__ARM_EABI__ 
		__ARM_ARCH_5__ 
		__ARM_ARCH_5T__ 
		__ARM_ARCH_5E__ 
		__ARM_ARCH_5TE__ 
		__ARM_ARCH_7__ 
		__ARM_ARCH_7A__)

	set_property(TARGET ${THIS_PROJECT} APPEND_STRING PROPERTY C_FLAGS "-g2 -gdwarf-2 -fpic -ffunction-sections -funwind-tables -fno-omit-frame-pointer -fno-strict-aliasing")
	set_property(TARGET ${THIS_PROJECT} APPEND_STRING PROPERTY CXX_FLAGS "-g2 -gdwarf-2 -fpic -ffunction-sections -funwind-tables -fno-omit-frame-pointer -fno-strict-aliasing")
	
	set_libpath_flag()
	set_property(TARGET ${THIS_PROJECT} APPEND_STRING PROPERTY LINK_FLAGS "-Wl,--allow-multiple-definition -Wl,--no-undefined -Wl,-z,relro -Wl,-z,now -Wl,-z,noexecstack -shared -Wl,-soname,\"libAndroidLauncher.so\"")
	#set_property(TARGET ${THIS_PROJECT} APPEND_STRING PROPERTY LINK_FLAGS_DEBUG " ${LIBPATH_FLAG}${SDK_DIR}/Scaleform/Lib/android-${CMAKE_ANDROID_ARCH}/Debug ${LIBPATH_FLAG}${SDK_DIR}Audio/wwise/SDK/android-9_${CMAKE_ANDROID_ARCH}/Debug/lib")
	#set_property(TARGET ${THIS_PROJECT} APPEND_STRING PROPERTY LINK_FLAGS_PROFILE " ${LIBPATH_FLAG}${SDK_DIR}/Scaleform/Lib/android-${CMAKE_ANDROID_ARCH}/Release ${LIBPATH_FLAG}${SDK_DIR}Audio/wwise/SDK/android-9_${CMAKE_ANDROID_ARCH}/Profile/lib")
	#set_property(TARGET ${THIS_PROJECT} APPEND_STRING PROPERTY LINK_FLAGS_RELEASE " ${LIBPATH_FLAG}${SDK_DIR}/Scaleform/Lib/android-${CMAKE_ANDROID_ARCH}/Release ${LIBPATH_FLAG}${SDK_DIR}Audio/wwise/SDK/android-9_${CMAKE_ANDROID_ARCH}/Release/lib")
	
	set_property(TARGET ${THIS_PROJECT} PROPERTY ANDROID_JAVA_SOURCE_DIR .)
	
	target_link_libraries(${THIS_PROJECT} PRIVATE m log c android)
	target_link_libraries(${THIS_PROJECT} PRIVATE SDL2)
	target_link_libraries(${THIS_PROJECT} PRIVATE SDLExt)

	#target_link_libraries(${THIS_PROJECT} PRIVATE CFilesForAndroidLauncher)

	add_dependencies(${THIS_PROJECT} ${MODULES} ${GAME_MODULES})
else()
	CryFileContainer(AndroidLauncher SOLUTION_FOLDER "Launcher")
endif()

