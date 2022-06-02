set(SRC_FILES
    ipc.cc
    visionipc_server.cc
    visionipc_client.cc
    visionbuf.cc
    visionbuf_cl.cc
)
if (BUILD_QCOM)
    list(APPEND SRC_FILES visionbuf_ion.cc)
endif()

add_library(visionipc SHARED ${SRC_FILES})
target_include_directories(visionipc PUBLIC . ..)

target_link_libraries(visionipc messaging cereal OpenCL::OpenCL)

add_cython_target(visionipc_pyx visionipc_pyx.pyx CXX)
add_library(visionipc_pyx MODULE ${visionipc_pyx})
target_include_directories(visionipc_pyx PRIVATE ${Python3_INCLUDE_DIRS} ${Python3_NumPy_INCLUDE_DIRS} ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/..)
target_link_libraries(visionipc_pyx PRIVATE ${Python3_LIBRARIES} visionipc)

if(BUILD_TESTING)
    add_executable(visionipc_test_runner
        test_runner.cc
        visionipc_tests.cc
    )
    target_include_directories(visionipc_test_runner PUBLIC .. .)
    target_link_libraries(visionipc_test_runner PUBLIC
        visionipc
        pthread
    )
    add_test(NAME visionipc_test_runner COMMAND visionipc_test_runner)
endif()
