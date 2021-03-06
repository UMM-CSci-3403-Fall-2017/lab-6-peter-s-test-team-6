Echo Server
===========

Starter code and tests for the Echo Client/Server lab.

This lab explores the idea of client-server organization.  In a client-server
configuration one central machine called the **server** acts as a central source
for some resource or service.  Other machines known as **clients** utilize the
resource or service provided by the server.  A good example would be a web-server
providing web-pages to multiple browsers on multiple computers.

The term **client-server** refers to the configuration of the service provider and
service consumer.  It does not require multiple machines. It is possible for the same
computer to act as server and as client.

Here we'll implement a simple, socket-based client-server system (the echo
client and server).

##Table of contents

-   [The echo client/server](#-the-echo-clientserver)
    -   [Bats Testing](#-testing)
    -   [Manual testing](#-manual-testing)

------------------------------------------------------------------------

### <span name="The_echo_client_server"></span> The echo client/server

Both as a way of gaining some experience with socket programming and as
example of building both ends of a client/server system, we'll build
what's called an *echo server*. An echo server is a server that echoes
back whatever it receives from a client. The starter code and tests are
in this Github repo,
which you should fork and complete.
:bangbang: You might find it useful to review and run this
[Date Server example](https://gist.github.com/NicMcPhee/2060037163d0d7fb475b5e4395b9ec32).
This sample program does many (but
not all) of the important things (including the socket work). The one
big difference there is that the `DateServer` returns a single string
and quits rather than continuing to echo back its input, but the socket
work there should be helpful. Your server should wait for a client
connection on port 6013 using `Socket.accept()`. When a connection is
made, the server should repeatedly:

-   Read data from the socket
-   Write that data back to the client

The server should continue this until the client breaks the connection.
After the connection is closed, the server should go back to listening
for new connections. Your server should be able to handle binary data as
well as text data (more on this below). Your *client* should take a
command line argument that is a hostname (e.g., `some.computer.edu`). The
server code will be assumed to be running on that host, and the client
will (try to) connect to that server. Note that if you get this right,
your client should be able to talk to any other group's server, and vice
versa. The recommended approach is to have the client repeatedly:

-   Read a single byte from the keyboard
-   Send a single byte to the server
-   Read a single byte from the server
-   Print that byte

#### <span name="Testing"></span> Testing

You have functional tests for this part of the lab, using the
[`bats` testing framework](https://github.com/sstephenson/bats).
In order for this to work
with Java, your project needs the specfic directory structure provided
in the starter repo. The code has three folders:

-   `src`, which holds the `.java` files where your code resides, and where the `.class` files will live when the tests run your code.
-   `test`, which has three `bats` test scruots, along
    with two other things: an `etc` folder which has several sample
    files you could use to test your code manually (see below), and a
    `sampleBin` folder that has working class file versions of the Echo
    Server and Echo Client, so you can test whether only one side has a
    problem.

The three test scripts are:

* `Echo_client.bats`, which tests your _client_ but uses the server code in `sampleBin`. You can use this to test your client without having implemented your server yet, or use it to help isolate whether a problem is in your client or your server.
* `Echo_server.bats`, which tests your _server_ but uses the client code in `sampleBin`. You can use this to test your server without having implemented your client yet, or use it to help isolate problems again.
* `Echo_serves_and_clients.bats`, which runs tests using both your client and server (so without using any of the code in `sampleBin`). Ultimately this is what you want to be able to run and have pass.

##### <span name="Manual_testing"></span> Manual testing

You can manually test your server with

```bash
telnet <hostname> 6013
```

This connects to the specified host at port 6013 (the port we're using).
You can then type things, and they'll be sent to the specified host
(which should be your Echo Server, which should just send it back). You
can also manually test if your server and client work with binary data
(which is the trickiest part of the lab). Let's assume that you've
started your echo server on some machine. Let's also assume that you've
got your echo client compiled and ready to run. Then on the
command line go to the directory that has your class files; this will
probably be your `src` directory if you're doing everything "by hand"
(i.e., not using Eclipse) and will be `bin/src` in your project
directory if you are using Eclipse. Then, assuming your client is a
class `EchoClient` in a package `echoserver`, run your client

```bash
java echoserver.EchoClient < test.jpg > output.jpg
```

This should (if everything's working) send the contents of "test.jpg" to
to the server, which should send them back, and then your client will
write them to standard output, which in this case is redirected into
"output.jpg". You can then use

```bash
diff test.jpg output.jpg
```

to see if the newly generated output file (`output.jpg`) is in fact
identical to the input file (`test.jpg`). You can do this with any
binary file (JPEGs, MP3s, Java class files, etc.); JPEGs have the
advantage that you can often open incomplete JPEG files and they'll just
show a monochrome block (usually in the bottom right corner) that
corresponds to the missing data. Since a common problem with this
problem is failure to deliver all the data, this allows you to see that
most of your system was working, but that you lost a bit on the end.
