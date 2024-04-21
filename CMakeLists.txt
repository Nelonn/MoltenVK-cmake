project(MoltenVK LANGUAGES OBJC OBJCXX C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(MoltenVK-1.2.8-rc1/External/cereal)
add_subdirectory(MoltenVK-1.2.8-rc1/External/glslang)
add_subdirectory(MoltenVK-1.2.8-rc1/External/SPIRV-Cross)


# Common

file(GLOB_RECURSE SOURCES_1 MoltenVK-1.2.8-rc1/Common/**.mm MoltenVK-1.2.8-rc1/Common/**.h)

add_library(MoltenVK_common STATIC ${SOURCES_1})
add_library(MoltenVK::Common ALIAS MoltenVK_common)

target_include_directories(MoltenVK_common PUBLIC MoltenVK-1.2.8-rc1/Common)


# MoltenVK

file(GLOB_RECURSE SOURCES_2
        MoltenVK-1.2.8-rc1/MoltenVK/MoltenVK/**.def
        MoltenVK-1.2.8-rc1/MoltenVK/MoltenVK/**.cpp
        MoltenVK-1.2.8-rc1/MoltenVK/MoltenVK/**.mm
        MoltenVK-1.2.8-rc1/MoltenVK/MoltenVK/**.m
        MoltenVK-1.2.8-rc1/MoltenVK/MoltenVK/**.h
)

add_library(MoltenVK_MoltenVK STATIC ${SOURCES_2})
add_library(MoltenVK::MoltenVK ALIAS MoltenVK_MoltenVK)
add_library(Vulkan::MoltenVK ALIAS MoltenVK_MoltenVK)

target_include_directories(MoltenVK_MoltenVK PUBLIC MoltenVK-1.2.8-rc1/MoltenVK/include)
target_include_directories(MoltenVK_MoltenVK PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})

target_compile_definitions(MoltenVK_MoltenVK PRIVATE "-DMVK_FRAMEWORK_VERSION=1.2.8")

file(GLOB_RECURSE INCLUDE_2 LIST_DIRECTORIES true MoltenVK-1.2.8-rc1/MoltenVK/MoltenVK/*)
target_include_directories(MoltenVK_MoltenVK PRIVATE ${INCLUDE_2} MoltenVK-1.2.8-rc1/MoltenVKShaderConverter)

target_link_libraries(MoltenVK_MoltenVK PUBLIC
        MoltenVK::Common
        cereal
        spirv-cross-core
        spirv-cross-glsl
        spirv-cross-cpp
        spirv-cross-reflect
        spirv-cross-hlsl
        spirv-cross-msl
        spirv-cross-util
        spirv-cross-c
)


# MoltenVKShaderConverter

file(GLOB_RECURSE SOURCES_3
        MoltenVK-1.2.8-rc1/MoltenVKShaderConverter/MoltenVKShaderConverter/**.def
        MoltenVK-1.2.8-rc1/MoltenVKShaderConverter/MoltenVKShaderConverter/**.cpp
        MoltenVK-1.2.8-rc1/MoltenVKShaderConverter/MoltenVKShaderConverter/**.mm
        MoltenVK-1.2.8-rc1/MoltenVKShaderConverter/MoltenVKShaderConverter/**.m
        MoltenVK-1.2.8-rc1/MoltenVKShaderConverter/MoltenVKShaderConverter/**.h
)

add_library(MoltenVK_MoltenVKShaderConverter STATIC ${SOURCES_3})
add_library(MoltenVK::MoltenVKShaderConverter ALIAS MoltenVK_MoltenVKShaderConverter)

target_include_directories(MoltenVK_MoltenVKShaderConverter PUBLIC MoltenVK-1.2.8-rc1/MoltenVKShaderConverter/include)
target_include_directories(MoltenVK_MoltenVKShaderConverter PRIVATE MoltenVK-1.2.8-rc1/MoltenVKShaderConverter)

target_link_libraries(MoltenVK_MoltenVKShaderConverter PUBLIC
        MoltenVK::Common
        glslang
        SPIRV
        SPIRV-Tools-opt
        spirv-cross-core
        spirv-cross-glsl
        spirv-cross-cpp
        spirv-cross-reflect
        spirv-cross-hlsl
        spirv-cross-msl
        spirv-cross-util
        spirv-cross-c
        "-framework Metal"
        "-framework AppKit"
        "-framework QuartzCore"
        "-framework Foundation"
        "-framework CoreFoundation"
        "-framework CoreGraphics"
        "-framework IOKit"
        "-framework IOSurface"
)
glslang_add_build_info_dependency(MoltenVK_MoltenVKShaderConverter)
