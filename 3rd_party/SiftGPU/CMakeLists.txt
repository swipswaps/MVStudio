if(NOT IS_MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")
endif()

add_definitions("-DSIFTGPU_NO_DEVIL")

set(SIFT_GPU_SOURCE_FILES
    FrameBufferObject.cpp
    FrameBufferObject.h
    GlobalUtil.cpp
    GlobalUtil.h
    GLTexImage.cpp
    GLTexImage.h
    ProgramGLSL.cpp
    ProgramGLSL.h
    ProgramGPU.h
    PyramidGL.cpp
    PyramidGL.h
    ShaderMan.cpp
    ShaderMan.h
    SiftGPU.cpp
    SiftGPU.h
    SiftMatch.cpp
    SiftMatch.h
    SiftPyramid.cpp
    SiftPyramid.h
)

if(CUDA_ENABLED)
    add_definitions("-DCUDA_SIFTGPU_ENABLED")

    set(SIFT_GPU_SOURCE_FILES
        ${SIFT_GPU_SOURCE_FILES}
        CuTexImage.cpp
        CuTexImage.h
        ProgramCU.cu
        ProgramCU.h
        PyramidCU.cpp
        PyramidCU.h
        SiftMatchCU.cpp
        SiftMatchCU.h
    )

    COLMAP_ADD_STATIC_CUDA_LIBRARY(sift_gpu ${SIFT_GPU_SOURCE_FILES})
else()
    add_library(sift_gpu STATIC ${SIFT_GPU_SOURCE_FILES})
endif()

target_link_libraries(sift_gpu
    ${SIFT_GPU_LIBRARIES}
    ${OPENGL_gl_LIBRARY}
    ${OPENGL_glu_LIBRARY}
)

set(GLEW_INCLUDE_DIR ${MVSTUDIO_THIRD_PARTY}/glew/include)
target_include_directories(sift_gpu PRIVATE ${GLEW_INCLUDE_DIR})
target_link_libraries(sift_gpu glew)
target_compile_definitions(sift_gpu PRIVATE GLEW_STATIC)