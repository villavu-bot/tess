cmake_minimum_required(VERSION 3.2)
project(Tesseract DESCRIPTION "libtesseract")
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_OSX_DEPLOYMENT_TARGET "10.13" CACHE STRING "Minimum OS X deployment version" FORCE)
add_library(${PROJECT_NAME} SHARED main.cpp exports.cpp)

IF(WIN32)
    target_link_libraries(${PROJECT_NAME} 
        -static-libgcc -static-libstdc++ 
        -Wl,--whole-archive -Wl,--export-all-symbol libtesseract.a libleptonica.a -Wl,--no-whole-archive
    )
ELSEIF(APPLE)
    target_include_directories(${PROJECT_NAME} /usr/local/include/)
    target_link_libraries(${PROJECT_NAME}
      "-framework accelerate"
      )
    target_link_libraries(${PROJECT_NAME} -static -force_load /usr/local/lib/libtesseract.a /usr/local/lib/libleptonica.a)
ELSE()
    target_link_libraries(${PROJECT_NAME} 
        -static-libgcc -static-libstdc++ 
        -Wl,--whole-archive libtesseract.a libleptonica.a -Wl,--no-whole-archive
    )
ENDIF()

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "lib")
    set_target_properties(${PROJECT_NAME} PROPERTIES OUTPUT_NAME "tesseract64")
elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
    set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "lib")
    set_target_properties(${PROJECT_NAME} PROPERTIES OUTPUT_NAME "tesseract32")
endif()
