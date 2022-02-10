---
title: Use SNOPT as A ROS Service
date: 2020-05-08 11:37:08
tags: 
  - ROS
  - Optimization
categories: Robot
---

As a mathematical solver, SNOPT is good enough to solve optimization problems such as **L**inear-**Q**uadratic-**R**egulator and **M**odel-**P**redictive-**C**ontroller. In ROS framework, 
packing SNOPT as a service is an efficient method to get the optimized result. Here we will introduce the whole configuraiton and setting step by step.

# Build a Server(C++)

In order to use SNOPT service, a server is required to receive the request and response the result.

{% note info %}
Actually, a specified `srv` file is also needed to transfer information in the interactive process. The tutorial that creates a `srv` file could be seen at [this link](https://sophistt.github.io/linux/2020/04/26/ros-services).
{% endnote %}

Here we will use the subroutines of snOptA in the book *User's Guide for SNOPT Version 7: Software for Large-Scale Nonlinear Programming* as the example to build the SNOPT server. 
The `srv` file is described as following:
```bash
# snopt_msgs/SnoptCal

float64 c1
float64 c2
---
float64[] x
```

## Code

The full code of the server node could be seen at [this link](). Here we only describe some significant code blocks.

### Head file and Static Variables

In order to use `snOptA` class, a specified **head file** is required and we also need to define some static variables for **Callback function**.
```cpp

#include "snopt_msgs/SnoptCal.h"
#include "snoptProblem.hpp"

// SNOPT Constraints and Variables
static snoptProblemA sp;

static double c1, c2;

static int n, neF, nS, nInf;
static double sInf;
static double *x, *xlow, *xupp, *xmul;
static double *F, *Flow, *Fupp, *Fmul;
static int *xstate, *Fstate;
static int    ObjRow;
static double ObjAdd;

const static int Cold = 0, Basis = 1, Warm = 2;
```

### main function 
In the `main` function, a ROS node and a ROS server are created. The snOptA problem is also initialized here with defining the constraints according to our own optimization problems.
```cpp
int main(int argc, char **argv)
{   
    // Ros Node Init
    ros::init(argc, argv, "snopt_server");
    ros::NodeHandle nh;
    ros::ServiceServer service = nh.advertiseService("snopt", snopt_callback);

    // SNOPT Init
    sp.initialize("", 1);
    sp.setIntParameter("Deriviative", 0);
    sp.setIntParameter("Verify level", 3);

    // SNOPT Variables and Constraints Init (n, neF, x, F)
    ... 

    ros::spin();
    return 0;
}
```

### Callback funtion and Optimization Problem

Callback function is the most import part in the server. In this function, we need to update the state of SNOPT problem through **request** from the client and solve the optimization problem.
At the end, return the solved result to the client through `srv`. 


```cpp
bool snopt_callback(snopt_msgs::SnoptCal::Request &req,
		    snopt_msgs::SnoptCal::Response $res)
{
    c1 = req.c1;
    c2 = req.c2;
    
    // Solve the optimization problem
    sp.solve(Cold, neF, n, objAdd, ObjRow, solveUsrf,
	     xlow, xupp, Flow, Fupp,
	     x, xstate, xmul, F, Fstate, Fmul,
	     ns, nInf, sInf);

    res.x.clear();
    for(int i = 0; i < n; i++)
        res.x.pushback(x[i]);

    return true;
}
```

The optimization problem has its own function prototype in `snoptProblem.hpp`, we use its prototype and define our own optimization problem in it.

```cpp

void solveUsrf(int *Status, int   *n, double x[],
	       int  *needf, int *neF, double F[],
	       int  *needG, int *neG, double G[],
	       char    *cu, int *lencu,
	       int    iu[], int *leniu,
	       double ru[], int *lenru)
{
    F[0] = x[1];
    F[1] = x[0] * x[0] + c1 * x[1] * x[1];
    F[2] = (x[0] - c2) * (x[0] - c2) + x[1] * x[1];
}
```

# Build a Client(C++)

Compared to the server, the client node is more straightforward, which only need to send request to the server through `srv`.
```cpp
int main(int argc, char **argv)
{
    ros::init(argc, argv, "snopt_client");
    ros::NodeHandle nh;
    ros::ServiceClient client = nh.serviceclient<snopt_msgs::SnoptCal>("snopt");

    // Update request
    snopt_msgs::SnoptCal srv;
    srv.request.c1 = atoll(argv[1]);
    srv.request.c2 = atoll(argv[2]);
    
    // Send request throgh client.call()
    if ( client.call(srv) )
         ROS_INFO("x[0]: %ld, x[1]: %ld", (long int)srv.response.x[0], (long int)srv.response.x[1]);

    return 0;
}
```

# Modify the CMakeLists.txt

To bulid and link the executable file successfully, the final step is modifying the `CMakeLists.txt` file. The example is showed as following:

```cmake

cmake_minimum_required(VERSION 2.8.3)
project(snopt_service)

## Compile as C++11, supported in ROS Kinetic and newer
add_compile_options(-std=c++11)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  snopt_msgs
)

set(SNOPT_DIR /path/to/SNOPT)
set(SNOPT_INCLUDE_DIR ${SNOPT_DIR}/include)

catkin_package(
   INCLUDE_DIRS
   LIBRARIES
   CATKIN_DEPENDS
)

include_directories(
  include/snopt_service
  message_runtime
  ${catkin_INCLUDE_DIRS}
  ${SNOPT_INCLUDE_DIR}
)

find_library(SNOPT_LIB1 libsnopt7_cpp.so ${SNOPT_DIR}/lib)

# Declare a C++ executable
add_executable(snopt_client src/snopt_client.cpp)
target_link_libraries(snopt_client ${catkin_LIBRARIES})
add_dependencies(snopt_client snopt_msgs_generate_messages_cpp)

add_executable(snopt_server src/snopt_server.cpp)
target_link_libraries(snopt_server ${catkin_LIBRARIES} ${SNOPT_LIB1})
add_dependencies(snopt_server snopt_msgs_generate_messages_cpp)
```

# References

- [User's Guide for SNOPT Version 7: Software for Large-Scale Nonlinear Programming](https://web.stanford.edu/group/SOL/guides/sndoc7.pdf)
- [ROS Services](https://wiki.ros.org/roscpp/Overview/Services)