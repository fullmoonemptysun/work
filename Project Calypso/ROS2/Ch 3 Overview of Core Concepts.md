
**Running a node**
- ros2 cmd line tool
- ros2 run \<package> \<executable>
	what package is the node in and which executable is the node.

- rqt_graph, we run this and see:
	![[Pasted image 20260617011327.png]]
	the two nodes are communicating through some communication channel in ROS2.

- ![[Pasted image 20260617015628.png]]
rqt_graph for turtlesim and teleop_key nodes

What can we get from those two experiments? As you can see, a ROS 2 node can be any kind of computer program that contains:
	Instructions to print logs in the terminal
	Graphical windows (2D, can also be 3D)
	Hardware drivers and more
A node, on top of being a computer program, also benefits from the ROS 2 functionalities: logs, communication with other nodes, and other features we will discover throughout this book.



## Topics

How nodes communicate with each other: Topics, services, actions.

![[Pasted image 20260619021322.png]]

- /chatter is the topic. Talker is publishing to that topic and listener is subscribing to that topic and so it receives everything talker publishes to the topic.


- Topic has a name and a type. 
`ros2 topic info <topic-name>`

- The type of a topic is its interface. 
`ros2 interface show <interface-name>` 
A contract that defines the structure of message to be published and to be expected by subscribers.

- The interface (contract) may contain many fields of different data types which together become the data being published and subscribed to. 

## Services

- A client server relationship. A node becomes a server and listens to provide a service
- Any node can then contact it as a client and get the services performed.

`ros2 service list`
- list of services running.
`ros2 service type <service_name>`
- like topics, services have interfaces with names, data types but the interfaces have two parts: request (data type) and response (data type) separated by `---`

- We can test a service without having a whole node calling it straight from the terminal:
`ros2 service call <service_name> <interface_name> "<request_in_json>"`


## Actions
- Basically services but meant for longer tasks that we might want more control (in between completion) over. 
- Moving robots, a sequence of steps. 
- Services are more for instant computations, immediate actions (kill).

- Name and interface: 3 parts: goal, result, feedback.
`ros2 action list`
`ros2 action info <action_name> -t` gives the type info
`ros2 action type <action_name>` does the same

We send the goal like:
`ros2 action send_goal <action_name> <interface> "<goal_in_json>"`

Returns the result. 

May give optional feedback at the end or in between the action.


## Parameters

Parameters given to nodes. 
`ros2 param list` shows running nodes and their params
`ros2 param get <node_name> <param_name>` gives info on the value and type of the param.

`ros2 run <package> <node_executable> --ros-args -p <param_name>:=<value> -p <param_name>:=<value> -p <.....` to start a node with certain parameter values.


## Launch files
Scripts that launch multiple nodes with their parameters in one go. (in the same terminal).

`ros2 launch package <launch_file_name>`





