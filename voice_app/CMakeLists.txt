cmake_minimum_required(VERSION 3.5)
project(voice_app)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

add_executable(iat_publish
  src/iat_publish.cpp
  src/speech_recognizer.c
  src/linuxrec.c)
target_link_libraries(iat_publish
  ${catkin_LIBRARIES}
  libmsc.so -ldl -lpthread -lm -lrt -lasound)
ament_target_dependencies(iat_publish rclcpp std_msgs)

add_executable(tts_subscribe src/tts_subscribe.cpp)
target_link_libraries(
  tts_subscribe
  ${catkin_LIBRARIES}
  libmsc.so -ldl -pthread)
ament_target_dependencies(tts_subscribe rclcpp std_msgs)

add_executable(voice_assistant src/voice_assistant.cpp)
target_link_libraries(
  voice_assistant
  ${catkin_LIBRARIES}
  libmsc.so -ldl -pthread)
ament_target_dependencies(voice_assistant rclcpp std_msgs)

install(TARGETS
  iat_publish
  tts_subscribe
  voice_assistant
  DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY launch
  DESTINATION share/${PROJECT_NAME})
  
ament_package()
