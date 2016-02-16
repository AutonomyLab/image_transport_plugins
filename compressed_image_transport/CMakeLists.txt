cmake_minimum_required(VERSION 2.8.3)
project(compressed_image_transport)

set(CUSTOM_OPENCV_DIR "/home/autolab/opencv/opencv-3.1.0/install")
find_package(OpenCV 3 REQUIRED PATHS ${CUSTOM_OPENCV_DIR} NO_DEFAULT_PATH)
message(STATUS "OpenCV Includes: ${OpenCV_INCLUDE_DIRS}")
message(STATUS "OpenCV Libraries: ${OpenCV_LIBRARIES}")
message(STATUS "OpenCV Libs: ${OpenCV_LIBS}")
message(STATUS "OpenCV Lib dir: ${OpenCV_LIB_DIR}")

find_package(catkin REQUIRED COMPONENTS cv_bridge dynamic_reconfigure image_transport)

# generate the dynamic_reconfigure config file
generate_dynamic_reconfigure_options(cfg/CompressedPublisher.cfg cfg/CompressedSubscriber.cfg)

catkin_package(
  INCLUDE_DIRS  ${OpenCV_INCLUDE_DIRS} include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS cv_bridge dynamic_reconfigure image_transport
  DEPENDS OpenCV3
)

include_directories(include ${OpenCV_INCLUDE_DIRS} ${catkin_INCLUDE_DIRS})
#include_directories(SYSTEM ${OpenCV_INCLUDE_DIRS})

add_library(${PROJECT_NAME} src/compressed_publisher.cpp src/compressed_subscriber.cpp src/manifest.cpp)
add_dependencies(${PROJECT_NAME} ${PROJECT_NAME}_gencfg)
target_link_libraries(${PROJECT_NAME} ${catkin_LIBRARIES} ${OpenCV_LIBRARIES})

class_loader_hide_library_symbols(${PROJECT_NAME})

install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

# add xml file
install(FILES compressed_plugins.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
