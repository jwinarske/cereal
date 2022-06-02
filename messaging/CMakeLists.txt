
add_library(messaging SHARED 
    messaging.cc
    impl_zmq.cc
    impl_msgq.cc
    msgq.cc
    socketmaster.cc
)
target_include_directories(messaging PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/..)
target_link_libraries(messaging PRIVATE 
    ${ZMQ_LINK_LIBRARIES}
    CapnProto::capnp
    CapnProto::kj
)
add_dependencies(messaging SERVICES_H)

add_executable(bridge bridge.cc)
target_include_directories(bridge PUBLIC ..)
target_link_libraries(bridge PUBLIC common messaging json11 ${ZMQ_LINK_LIBRARIES})
add_dependencies(bridge SERVICES_H)

add_cython_target(messaging_pyx messaging_pyx.pyx CXX)
add_library(messaging_pyx MODULE ${messaging_pyx})
target_include_directories(messaging_pyx PRIVATE ${Python3_INCLUDE_DIRS} ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(messaging_pyx PRIVATE ${Python3_LIBRARIES} messaging ${ZMQ_LINK_LIBRARIES} common)

if(BUILD_TESTING)
    add_executable(messaging_test_runner
        test_runner.cc
        msgq_tests.cc
    )
    target_link_libraries(messaging_test_runner PUBLIC messaging common)
    add_test(NAME messaging_test_runner COMMAND messaging_test_runner)
endif()
