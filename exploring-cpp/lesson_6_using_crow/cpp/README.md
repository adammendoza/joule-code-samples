## Lesson 6: Using Crow

This lesson is focused on creating a small webserver using the Crow micro-framework for web. Ensure that you’ve opened your IP tables in order to access the server!

### The Software

In this lesson you won't need a circuit at all, but if you're planning on continuing it would be fine to leave your circuit from lesson 5 together for the moment. This lesson will be focusing on Crow, a lightweight micro-framework for displaying HTML webpages. We can use it to retrieve feedback outside of the console, in order to allow the board to do what you need it to remotely. In order to build applications using Crow you need to install it. So let's get to it!

#### Installing Crow

##### On Target

In order to build Crow, we need to install Cmake. Make sure you have an internet connection before you get started. Go ahead and enter the following commands to get Cmake installed.  Note, the last command can take over 15 minutes, be patient.

```bash
cd
git clone https://github.com/Kitware/CMake
cd CMake
./bootstrap && make && make install
```

This should install Cmake for you and allow you to install anything that uses Cmake! Now we just need to install Crow. Use the following commands to do so. Note: the cmake step below can take over 15 minutes so be patient.

```bash
cd
git clone https://github.com/ipkn/crow
cd crow
mkdir build
cd build
cmake ..
make
cd amalgamate
cp crow_all.h /usr/include/
cd
```

##### Using ISS

First create a project using the Joule platform, then in a linux console type:

`docker ps`

You should see a list of running Docker containers.

Now open a session inside the container.

`docker exec –ti <copy Ostro container id> /bin/bash`

You can now follow the same steps to install crow (or any other library) Type exit to leave the session, and then commit the changes

`docker commit <copy Ostro container id> inteliotdevkit/intel-iot-ostro:latest`

This will commit the changes to the image. Your Ostro container will now contain these changes.

#### The Code

Now let's get to the code!

We begin by including `crow_all.h` which allows us to use the Crow functions and server. In order to start the server we call `crow::SimpleApp app;`. That's the actual server, ready to be loaded with all sorts of information to customize the server for your needs. We'll be using the file `lesson_6_index.html`. The following block of code does several things, so let's take a look at it.

```cpp
	CROW_ROUTE(app, "/")([](){
		crow::mustache::context ctx;
		return crow::mustache::load("lesson_6_index.html").render();
	});
```

As convoluted as this seems, it simply builds up the server, using the file `lesson_6_index.html`. This means we're ready to start the server. We do so by simply saying `app.port(80).run();`. The server will now be up and running! 

Now ensure to use the proper compiler flags when you attempt to compile this program!

`g++ -std=c++11 -lpthread -lboost_system lesson_6.cpp -o lesson_6`

Once you've compiled the program, you'll need to get your boards IP address. The device you want to use to access the web server will need to be on the same network as your board. To look up your IP address, use the `ifconfig` command. Now you can start up the program, which will start the Crow server. Connect to it by typing in the IP address you got from your `ifconfig` command, and you should see the HTML page!

Now run the program using the following command.

`./lesson_6`

### Troubleshooting

If you have any issues with the program compiling, ensure that your compiler flags are correct.

If you can't access your webserver there are a few problems that can generally happen. First, you must make sure that your device is on the same network as your board. Finally, ensure that your board's ports are correctly opened. Do so by running the following code.

```bash
iptables -F
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -S
```

This should set everything up properly.

### Tested
|	Operating System	|	Carrier Board	|	mraa Version	|
|:---------------------:|:-----------------:|:-----------------:|
|	Ostro 166			|Intel Stock Board	|	mraa 1.2.3		|
|	Ostro 166 			|Intel Stock Board	|	mraa 1.2.2		|
