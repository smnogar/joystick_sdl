cmake_minimum_required(VERSION 2.8.3)
project(joystick_sdl)

 # Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++11)

find_package(SDL2 REQUIRED)

# set default build type
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE RelWithDebInfo)
endif()

find_package(catkin REQUIRED COMPONENTS roscpp diagnostic_updater sensor_msgs cmake_modules) 

include_directories( include
  ${SDL2_INCLUDE_DIRS}
  ${catkin_INCLUDE_DIRS}
)

catkin_package(
  INCLUDE_DIRS
  LIBRARIES
  CATKIN_DEPENDS roscpp diagnostic_updater sensor_msgs
  DEPENDS SDL2)


add_library(${PROJECT_NAME} src/joystick.cpp)
target_link_libraries(${PROJECT_NAME} ${SDL2_LIBRARIES} ${catkin_LIBRARIES})
add_dependencies(${PROJECT_NAME} ${catkin_EXPORTED_TARGETS})

if(SDL204_FOUND)
  set_property(TARGET ${PROJECT_NAME} PROPERTY COMPILE_DEFINITIONS SDL2_ENABLE_JOYSTICK_POWER_LEVEL)
endif()

add_executable(${PROJECT_NAME}_node src/joystick_node)
target_link_libraries(${PROJECT_NAME}_node ${PROJECT_NAME} ${SDL2_LIBRARIES} ${catkin_LIBRARIES})

install(TARGETS ${PROJECT_NAME} ${PROJECT_NAME}_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION})

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

install(DIRECTORY mappings
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})

if (CATKIN_ENABLE_TESTING)
  find_package(roslint REQUIRED)

  roslint_cpp()
  roslint_add_test()
endif()

