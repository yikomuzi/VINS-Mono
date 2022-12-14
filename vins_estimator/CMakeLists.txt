cmake_minimum_required(VERSION 2.8.3)
project(vins_estimator)

#【解决】很奇怪，编译的时候必须使用release模式，debug模式回环检测部分总是失效
#vins_estimator必须使用Release模式才能成功回环检测，不过回环检测效果会相对变差，其他都可以使用debug模式，感觉可能是topic发布的问题
set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-std=c++11")
#-DEIGEN_USE_MKL_ALL")
#set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")

find_package(catkin REQUIRED COMPONENTS
        roscpp
        std_msgs
        geometry_msgs
        nav_msgs
        tf
        cv_bridge
        )

find_package(OpenCV REQUIRED)
message(-------------------------------------OpenCV_VERSION:${OpenCV_VERSION})

# message(WARNING "OpenCV_VERSION: ${OpenCV_VERSION}")

find_package(Ceres REQUIRED)

include_directories(${catkin_INCLUDE_DIRS} ${CERES_INCLUDE_DIRS})

set(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)
find_package(Eigen3)
include_directories(
        ${catkin_INCLUDE_DIRS}
        ${EIGEN3_INCLUDE_DIR}
)

message(------------------------------------------catkin_INCLUDE_DIRS:${catkin_INCLUDE_DIRS})

catkin_package()

add_executable(vins_estimator
        src/estimator_node.cpp
        src/parameters.cpp
        src/estimator.cpp
        src/feature_manager.cpp
        src/factor/pose_local_parameterization.cpp
        src/factor/projection_factor.cpp
        src/factor/projection_td_factor.cpp
        src/factor/marginalization_factor.cpp
        src/utility/utility.cpp
        src/utility/visualization.cpp
        src/utility/CameraPoseVisualization.cpp
        src/initial/solve_5pts.cpp
        src/initial/initial_aligment.cpp
        src/initial/initial_sfm.cpp
        src/initial/initial_ex_rotation.cpp
        )


target_link_libraries(vins_estimator ${catkin_LIBRARIES} ${OpenCV_LIBS} ${CERES_LIBRARIES})
message(----------------------------------------------catkin_LIBRARIES:${catkin_LIBRARIES})


