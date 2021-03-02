# Part 1

## Step 1

<details>
<summary>CMakeLists.txt</summary>

    cmake_minimum_required(VERSION 3.10)
    
    # set the project name and version
    project(Tutorial VERSION 1.0)
    
    # specify the C++ standard
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED True)
    
    # configure a header file to pass some of the CMake settings
    # to the source code
    configure_file(TutorialConfig.h.in TutorialConfig.h)
    
    # add the executable
    add_executable(Tutorial tutorial.cxx)
    
    # add the binary tree to the search path for include files
    # so that we will find TutorialConfig.h
    target_include_directories(Tutorial PUBLIC
                               "${PROJECT_BINARY_DIR}"
                               )


Tutorial.cxx
    
    // A simple program that computes the square root of a number
    #include <cmath>
    // #include <cstdlib>
    #include <iostream>
    #include <string>
    #include "TutorialConfig.h"
    
    int main(int argc, char* argv[])
    {
      if (argc < 2) {
        // report version
        std::cout << argv[0] << " Version " << Tutorial_VERSION_MAJOR << "."
                  << Tutorial_VERSION_MINOR << std::endl;
        std::cout << "Usage: " << argv[0] << " number" << std::endl;
        return 1;
      }
    
      // convert input to double
      const double inputValue = std::stod(argv[1]);
    
    
      // calculate square root
      const double outputValue = sqrt(inputValue);
      std::cout << "The square root of " << inputValue << " is " << outputValue
                << std::endl;
      return 0;
    }
    
![Screenshot](https://github.com/gabeorlanski/oss-repo-template/blob/master/labs/lab-05/step1.PNG)


## Step 2

CMakeLists.txt
    cmake_minimum_required(VERSION 3.10)
    
    # set the project name and version
    project(Tutorial VERSION 1.0)
    
    # specify the C++ standard
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED True)
    
    # STEP 2:
    option(USE_MYMATH "Use tutorial provided math implementation" ON)
    
    
    # configure a header file to pass some of the CMake settings
    # to the source code
    configure_file(TutorialConfig.h.in TutorialConfig.h)
    
    
    # STEP 2:
    # Add in the math sqrt lib
    add_library(MathFunctions mysqrt.cxx)
    
    
    if(USE_MYMATH)
      add_subdirectory(MathFunctions)
      list(APPEND EXTRA_LIBS MathFunctions)
      list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")
    endif()
    
    
    # add the executable
    add_executable(Tutorial tutorial.cxx)
    
    
    target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})
    
    # add the binary tree to the search path for include files
    # so that we will find TutorialConfig.h
    target_include_directories(Tutorial PUBLIC
                               "${PROJECT_BINARY_DIR}"
                               ${EXTRA_INCLUDES}
                               )


tutorial.cxx
    
    // A simple program that computes the square root of a number
    #include <cmath>
    #include <iostream>
    #include <string>
    
    #include "TutorialConfig.h"
    #ifdef USE_MYMATH
    #  include "MathFunctions.h"
    #endif
    int main(int argc, char* argv[])
    {
      
      if (argc < 2) {
        // report version
        std::cout << argv[0] << " Version " << Tutorial_VERSION_MAJOR << "."
                  << Tutorial_VERSION_MINOR << std::endl;
        std::cout << "Usage: " << argv[0] << " number" << std::endl;
        return 1;
      }
    
      // convert input to double
      const double inputValue = std::stod(argv[1]);
    #ifdef USE_MYMATH
      const double outputValue = mysqrt(inputValue);
    #else
      const double outputValue = sqrt(inputValue);
    #endif
      std::cout << "The square root of " << inputValue << " is " << outputValue
                << std::endl;
      return 0;
    }

![Screenshot](https://github.com/gabeorlanski/oss-repo-template/blob/master/labs/lab-05/step2.PNG)


## Step 3

CMakeLists.txt
    
    cmake_minimum_required(VERSION 3.10)
    
    # set the project name and version
    project(Tutorial VERSION 1.0)
    
    # specify the C++ standard
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED True)
    
    # should we use our own math functions
    option(USE_MYMATH "Use tutorial provided math implementation" ON)
    
    # configure a header file to pass some of the CMake settings
    # to the source code
    configure_file(TutorialConfig.h.in TutorialConfig.h)
    
    # add the MathFunctions library
    if(USE_MYMATH)
      add_subdirectory(MathFunctions)
      list(APPEND EXTRA_LIBS MathFunctions)
    endif()
    
    # add the executable
    add_executable(Tutorial tutorial.cxx)
    
    target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})
    
    # add the binary tree to the search path for include files
    # so that we will find TutorialConfig.h
    target_include_directories(Tutorial PUBLIC
                               "${PROJECT_BINARY_DIR}"
                               )


MathFunctions/CMakeLists.txt
    
    add_library(MathFunctions mysqrt.cxx)
    
    target_include_directories(MathFunctions
    INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}    
    )


![ScreenShot](https://github.com/gabeorlanski/oss-repo-template/blob/master/labs/lab-05/step3.PNG)

## Step 4

CMakeLists.txt

    
    cmake_minimum_required(VERSION 3.10)
    
    # set the project name and version
    project(Tutorial VERSION 1.0)
    
    # specify the C++ standard
    set(CMAKE_CXX_STANDARD 11)
    set(CMAKE_CXX_STANDARD_REQUIRED True)
    
    # should we use our own math functions
    option(USE_MYMATH "Use tutorial provided math implementation" ON)
    
    # configure a header file to pass some of the CMake settings
    # to the source code
    configure_file(TutorialConfig.h.in TutorialConfig.h)
    
    # add the MathFunctions library
    if(USE_MYMATH)
      add_subdirectory(MathFunctions)
      list(APPEND EXTRA_LIBS MathFunctions)
    endif()
    
    # add the executable
    add_executable(Tutorial tutorial.cxx)
    
    target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})
    
    # add the binary tree to the search path for include files
    # so that we will find TutorialConfig.h
    target_include_directories(Tutorial PUBLIC
                               "${PROJECT_BINARY_DIR}"
                               )
    install(TARGETS Tutorial DESTINATION bin)
    install(FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"
      DESTINATION include
      )
    
      enable_testing()
    
      # does the application run
      add_test(NAME Runs COMMAND Tutorial 25)
      
      # does the usage message work?
      add_test(NAME Usage COMMAND Tutorial)
      set_tests_properties(Usage
        PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number"
        )
      
      # define a function to simplify adding tests
      function(do_test target arg result)
        add_test(NAME Comp${arg} COMMAND ${target} ${arg})
        set_tests_properties(Comp${arg}
          PROPERTIES PASS_REGULAR_EXPRESSION ${result}
          )
      endfunction(do_test)
      
      # do a bunch of result based tests
      do_test(Tutorial 4 "4 is 2")
      do_test(Tutorial 9 "9 is 3")
      do_test(Tutorial 5 "5 is 2.236")
      do_test(Tutorial 7 "7 is 2.645")
      do_test(Tutorial 25 "25 is 5")
      do_test(Tutorial -25 "-25 is [-nan|nan|0]")
      do_test(Tutorial 0.0001 "0.0001 is 0.01")


MathFunctions/CMakeLists.txt

    add_library(MathFunctions mysqrt.cxx)
    
    # state that anybody linking to us needs to include the current source dir
    # to find MathFunctions.h, while we don't.
    target_include_directories(MathFunctions
              INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
              )
    install(TARGETS MathFunctions DESTINATION lib)
    install(FILES MathFunctions.h DESTINATION include)

![Screenshot](https://github.com/gabeorlanski/oss-repo-template/blob/master/labs/lab-05/step4.PNG)


## Step 5

CMakeLists.txt

```
cmake_minimum_required(VERSION 3.10)

# set the project name and version
project(Tutorial VERSION 1.0)

# specify the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# should we use our own math functions
option(USE_MYMATH "Use tutorial provided math implementation" ON)

# configure a header file to pass some of the CMake settings
# to the source code
configure_file(TutorialConfig.h.in TutorialConfig.h)

# add the MathFunctions library
if(USE_MYMATH)
  add_subdirectory(MathFunctions)
  list(APPEND EXTRA_LIBS MathFunctions)
endif()

# add the executable
add_executable(Tutorial tutorial.cxx)
target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           )

# add the install targets
install(TARGETS Tutorial DESTINATION bin)
install(FILES "${PROJECT_BINARY_DIR}/TutorialConfig.h"
  DESTINATION include
  )

# enable testing
enable_testing()

# does the application run
add_test(NAME Runs COMMAND Tutorial 25)

# does the usage message work?
add_test(NAME Usage COMMAND Tutorial)
set_tests_properties(Usage
  PROPERTIES PASS_REGULAR_EXPRESSION "Usage:.*number"
  )

# define a function to simplify adding tests
function(do_test target arg result)
  add_test(NAME Comp${arg} COMMAND ${target} ${arg})
  set_tests_properties(Comp${arg}
    PROPERTIES PASS_REGULAR_EXPRESSION ${result}
    )
endfunction(do_test)

# do a bunch of result based tests
do_test(Tutorial 4 "4 is 2")
do_test(Tutorial 9 "9 is 3")
do_test(Tutorial 5 "5 is 2.236")
do_test(Tutorial 7 "7 is 2.645")
do_test(Tutorial 25 "25 is 5")
do_test(Tutorial -25 "-25 is [-nan|nan|0]")
do_test(Tutorial 0.0001 "0.0001 is 0.01")
```

MathFunctions/CMakeLists.txt

```
add_library(MathFunctions mysqrt.cxx)

# state that anybody linking to us needs to include the current source dir
# to find MathFunctions.h, while we don't.
target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )

# install rules
install(TARGETS MathFunctions DESTINATION lib)
install(FILES MathFunctions.h DESTINATION include)

include(CheckSymbolExists)
check_symbol_exists(log "math.h" HAVE_LOG)
check_symbol_exists(exp "math.h" HAVE_EXP)
if(NOT (HAVE_LOG AND HAVE_EXP))
  unset(HAVE_LOG CACHE)
  unset(HAVE_EXP CACHE)
  set(CMAKE_REQUIRED_LIBRARIES "m")
  check_symbol_exists(log "math.h" HAVE_LOG)
  check_symbol_exists(exp "math.h" HAVE_EXP)
  if(HAVE_LOG AND HAVE_EXP)
  target_compile_definitions(MathFunctions
                             PRIVATE "HAVE_LOG" "HAVE_EXP")
    endif()
endif()
```

![ScreenShot](https://github.com/gabeorlanski/oss-repo-template/blob/master/labs/lab-05/step5.PNG)


# Part 2

Makefile

```
# Create all
all: dynamic_block static_block


# Create the commands for the dynamic block
dynamic_block: dynamic_bin dynamic_lib

# Do the same but for the static lib
static_block: static_bin static_lib




# Create the main command to run program.c
program.o: program.c
	gcc -c program.c -o program.o



# Create the bins
dynamic_bin: dynamic_lib program.o
	gcc  program.o -Ldynamic -ldynamic -o dynamic_bin


# Create the bins
static_bin: static_lib program.o
	gcc  program.o -Lstatic -lstatic -o static_bin

#Create the .o file for source/block.c
block.o: source/block.c
	gcc -c -fPIC source/block.c -o block.o

# Create the dynamic lib lib 
dynamic_lib: block.o
	mkdir -p dynamic
  
  # Had to add in -Wl,-rpath . or else it would not work
	gcc -shared block.o -o dynamic/libdynamic.so -Wl,-rpath .

# Do the same(ish) for the shared lib
static_lib: block.o
	mkdir -p static
	ar rcs static/libstatic.a block.o


clean:
	rm -rf *.o *.a *.so dynamic_bin dynamic/ static/
```


CMakeLists.txt
```
cmake_minimum_required(VERSION 3.0)
project(lab5)
add_library(dynamic SHARED source/block.c)
add_library(static source/block.c)
add_executable(dynamic_c program.c)
target_link_libraries(dynamic_c dynamic)

add_executable(static_c program.c)
target_link_libraries(static_c static)
```

MakeFile created by CMake:

It is really long so I committed it. It is called THIS_IS_CMAKE_MAKE.txt. [Click this link.](https://github.com/gabeorlanski/oss-repo-template/blob/master/labs/lab-05/THIS_IS_CMAKE_MAKE.txt)


With Make:

Dynamic Is 16696 bytes and Static is 16856 Bytes

With CMake:


Dynamic Is 16696 bytes and Static is 16856 Bytes


![SCREENS](https://github.com/gabeorlanski/oss-repo-template/blob/master/labs/lab-05/part_2.PNG)