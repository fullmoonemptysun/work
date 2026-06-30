
Before anything else — what is a process?

Every program running on your computer is a process. Your terminal is a process. Each process has its own isolated memory. Variables set in one process don't automatically exist in another.

Now — what is a shell?

Bash is a process. When you open a terminal, bash starts as a fresh process with empty memory. It then reads `~/.bashrc` and executes every line in it to populate its memory with variables and settings.

Now — what is an environment variable in memory terms?

It's literally a key-value pair stored in your bash process's memory. `PATH=/usr/bin:/usr/local/bin` means bash has a variable named PATH whose value is that string. When you type `ros2`, bash looks through every directory listed in PATH until it finds an executable named `ros2`.

Now — what does `source` do at the lowest level?

`source filename` tells bash: read this file and execute every line directly inside your own process memory. Not a child process. Not a new terminal. Your current bash process.

So `source /opt/ros/jazzy/setup.bash` opens that file, reads lines like `export PATH="/opt/ros/jazzy/bin:$PATH"` and executes them directly in your bash process memory — adding ROS2's directories to your PATH right now in this session.