cmake_minimum_required(VERSION 3.2)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_OSX_DEPLOYMENT_TARGET "10.13" CACHE STRING "Minimum OS X deployment version" FORCE)

project(Tesseract DESCRIPTION "libtesseract")
add_library(${PROJECT_NAME} SHARED main.cpp exports.cpp)

IF(WIN32)
    target_link_options(${PROJECT_NAME} PRIVATE -static)
    target_link_libraries(${PROJECT_NAME} -pthread)
    target_link_libraries(${PROJECT_NAME}
        -static libtesseract.a libleptonica.a
    )
ELSEIF(APPLE)
    target_include_directories(${PROJECT_NAME} PUBLIC /usr/local/include/)
    target_link_libraries(${PROJECT_NAME}
      "-framework accelerate"
      )
    target_link_libraries(${PROJECT_NAME} /usr/local/lib/libtesseract.a /usr/local/lib/libleptonica.a)
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
