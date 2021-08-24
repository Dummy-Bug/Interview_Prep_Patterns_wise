# Socket Programming 

Socket programming is a way of connecting two nodes on a network to communicate with each other. One socket(node) listens on a particular port at an IP, while other socket reaches out to the other to form a connection. Server forms the listener socket while client reaches out to the server.

![img](https://media.geeksforgeeks.org/wp-content/uploads/Socket-Programming-in-C-C-.jpg)

#### Stages for server:

1. Socket creation

2. Setsockopt (This helps in manipulating options for the socket referred by the file descriptor sockfd - helps in reuse of address and port)

3. Bind (binds the socket to the address and port number specified in addr)

4. Listen (It puts the server socket in a passive mode, where it waits for the client to approach the server to make a connection)

5. Accept (It extracts the first connection request on the queue of pending connections for the listening socket, sockfd, creates a new connected socket, and returns a new file descriptor referring to that socket. At this point, connection is established between client and server, and they are ready to transfer data)


#### Stages for Client:

1. Socket connection

2. Connect (The connect() system call connects the socket referred to by the file descriptor sockfd to the address specified by addr. Server’s address and port is specified in addr)


-----


## Interview Questions

#### Q1. Differences between TCP and UDP 

![img](https://github.com/naman14310/Interview_Prep/blob/main/Project%20Notes/torrent/images/TCP%20vs%20UDP.png)

<br>

#### Q2. What does a socket consists of ?

A socket is a combination of IP address and Port Number

<br>

#### Q3. What are the advantages and Disadvantages of Sockets ?

Advantage: Sockets are flexible and sufficient. Efficient socket based programming can be easily implemented for general communications. It cause low network traffic.

Disadvantage: Socket based communications allows only to send packets of raw data between applications. Both the client-side and server-side have to provide mechanisms to make the data useful in any way.

<br>

#### Q4. Explain Data Transfer Over Connected Sockets - Send() And Recv() ?

Two additional data transfer library calls, namely send() and recv(), are available if the sockets are connected. They correspond very closely to the read() and write() functions used for I/O on ordinary file descriptors.

<br>

#### Q5. What is synchronous and asynchronous socket ?

The send, receive, and reply operations may be synchronous or asynchronous. A synchronous operation blocks a process till the operation completes. An asynchronous operation is non-blocking and only initiates the operation.

<br>

#### Q6. Which is faster synchronous or asynchronous ?

Asynchronous transfers are generally faster than synchronous transfers. This is because they do not take up time prior to the transfer to coordinate their efforts. However, because of this, more errors tend to occur in asynchronous transfers. Asynchronous programming is event-driven. You start an operation, but you don't know when it will end