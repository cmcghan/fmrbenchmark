cmake_minimum_required (VERSION 2.8.3)
project (dynamaestro)
add_compile_options (-std=c++11)

include_directories (include)

find_package (Boost REQUIRED COMPONENTS thread)
include_directories (${Boost_INCLUDE_DIRS})

find_package (Eigen3 REQUIRED)
include_directories (${EIGEN3_INCLUDE_DIR})

if (CATKIN_ENV)
  message (STATUS "Building for ROS")

  add_executable (log src/log.cpp)
  add_executable (dm src/main.cpp)
  target_link_libraries (dm ${Boost_LIBRARIES})

  add_definitions (-DUSE_ROS)
  find_package (catkin REQUIRED COMPONENTS roscpp tf integrator_chains_msgs)

  catkin_package ()
  include_directories (include ${catkin_INCLUDE_DIRS})
  target_link_libraries (log ${catkin_LIBRARIES})
  target_link_libraries (dm ${catkin_LIBRARIES})
  install (TARGETS dm log
    RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  )

  install (FILES launch/trivial-demo.launch
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  )
else (CATKIN_ENV)
  message (WARNING "Not all components can be built as standalone.")

  add_executable (hellopolytope examples/standalone/hellopolytope.cpp)
  add_executable (genproblem examples/standalone/genproblem.cpp)
endif (CATKIN_ENV)
