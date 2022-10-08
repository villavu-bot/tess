cmake_minimum_required(VERSION 3.2)
project(Tesseract DESCRIPTION "libtesseract")

set(CMAKE_CXX_STANDARD 17)

add_library(${PROJECT_NAME} SHARED main.cpp exports.cpp)

IF(WIN32)
    set(CMAKE_CXX_STANDARD_LIBRARIES "-static-libgcc -Wl,-Bstatic -lstdc++ -lpthread -Wl,-Bdynamic ${CMAKE_CXX_STANDARD_LIBRARIES}")
    target_link_libraries(${PROJECT_NAME}
        -Wl,--whole-archive libtesseract.a -Wl,--no-whole-archive libleptonica.a
    )
ELSEIF(APPLE)
    set(CMAKE_OSX_DEPLOYMENT_TARGET "10.13" CACHE STRING "Minimum OS X deployment version" FORCE)
   
    target_include_directories(${PROJECT_NAME} PUBLIC /usr/local/include/)
    target_link_directories(${PROJECT_NAME} PUBLIC /usr/local/lib/)
    target_link_libraries(${PROJECT_NAME}
      "-framework accelerate"
      )
    target_link_libraries(${PROJECT_NAME} -Wl,--whole-archive libtesseract.a -Wl,--no-whole-archive libleptonica.a)
ELSE()
    set(CMAKE_CXX_STANDARD_LIBRARIES "-static-libgcc -Wl,-Bstatic -lstdc++ -lpthread -Wl,-Bdynamic ${CMAKE_CXX_STANDARD_LIBRARIES}")
    target_link_libraries(${PROJECT_NAME}
        -Wl,--whole-archive libtesseract.a -Wl,--no-whole-archive libleptonica.a
    )
ENDIF()

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "lib")
    set_target_properties(${PROJECT_NAME} PROPERTIES OUTPUT_NAME "tesseract64")
elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
    set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "lib")
    set_target_properties(${PROJECT_NAME} PROPERTIES OUTPUT_NAME "tesseract32")
endif()
