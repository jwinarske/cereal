find_program(PYTHON3 python3 REQUIRED)

add_custom_target(SERVICES_H 
    BYPRODUCTS services.h
    COMMAND ${PYTHON3} services.py > services.h
    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
    VERBATIM
)

set(CAPNPC_OUTPUT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/gen/cpp" CACHE STRING "" FORCE)
file(MAKE_DIRECTORY "${CAPNPC_OUTPUT_DIR}")

capnp_generate_cpp(gen_source_files gen_headers log.capnp car.capnp legacy.capnp)
add_library(cereal SHARED ${gen_source_files})
add_dependencies(cereal SERVICES_H)

add_subdirectory(messaging)
add_subdirectory(visionipc)
