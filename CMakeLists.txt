cmake_minimum_required(VERSION 3.2)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_OSX_DEPLOYMENT_TARGET "10.13" CACHE STRING "Minimum OS X deployment version" FORCE)

#set(CMAKE_CXX_STANDARD_LIBRARIES "-static-libgcc -static-libstdc++ -lwsock32 -lws2_32 ${CMAKE_CSS_STANDARD_LIBRARIES}")
#set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -Wl,-Bstatic,--whole-archive -lwinpthread -Wl,--no-whole-archive")

project(Tesseract DESCRIPTION "libtesseract")
add_library(${PROJECT_NAME} SHARED main.cpp exports.cpp)

IF(WIN32)
    set(CMAKE_CXX_STANDARD_LIBRARIES "-static-libgcc -static-libstdc++ -Wl,-Bstatic -lpthread -Wl,-Bdynamic ${CMAKE_CXX_STANDARD_LIBRARIES}")

    #target_link_options(${PROJECT_NAME} PUBLIC -static-libgcc -static-libstdc++)
    target_link_libraries(${PROJECT_NAME}
        libtesseract.a libleptonica.a libwinpthread.a
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
