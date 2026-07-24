
## Creating and building a workspace

- create Projectnam/src/
- to build 
`colcon build`
colcon is the build tool for Ros2 comes with `ros2-dev-tools`

`build` will contain intermediate files required for the overall build, logs will contain build logs and install contains the final built nodes.

## Sourcing the workspace
`source path/to/directory/intall/setup.bash` in the bashrc file


## Creating a package
- Package is an independent unit of a robot (Camera, Vision, Motion planning, Hardware control)

`ros2 pkg create <pkg_name> (optional) --build-type ament_cmake/ament_python --dependencies dependency1 dep2 dep3....`

`package.xml` will store info about the package itself
`CMakeLists.txt` Used to provide instruction on how to compile the C++ nodes, create libraries and so on. 
`include` all the header files will go here. 


## Building a package
`colcon build` from the project dir. 
must source again to make the environment aware of all the new changes in the workspace. 

to build specific packages
`colcon build --package-select <package>`

## How to organize nodes in a package

- each node is one functionality 
![[Pasted image 20260702153527.png]]
example

## A node in C++
```cpp
#include "rclcpp/rclcpp.hpp"

using namespace std;

class MyCustomNode : public rclcpp::Node{ //rclcpp is the namespace (package name)

	public:
	
		MyCustomNode():Node("new_node"), counter(0){
		
		timer = this->create_wall_timer(chrono::seconds(5), bind(&MyCustomNode::print_hello, this));
		
		}
		
		void print_hello(){
		
		RCLCPP_INFO(this->get_logger(), "hello %d", counter);
		
		counter++;

}

	private:
	
		int counter;
		
		rclcpp::TimerBase::SharedPtr timer;//type alias for std::shared_ptr<rclcpp::TimerBase> timer;

};

int main(int argc, char** argv){

	rclcpp::init(argc, argv);
	
	auto node = make_shared<MyCustomNode>(); //shared pointer
	
	rclcpp::spin(node);
	
	rclcpp::shutdown();
	
	return 0;

}
```



`ros2 node list`
`ros2 node info <node_name>` 
to know about nodes.


`ros2 run my_py_pkg test_node --ros-args -r __node:=abc` to change the name of the node from the one in the code.


---
