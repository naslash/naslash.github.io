---
layout: page
title: TCP/IP Network Overlay of Minimum Spanning Trees in Java
permalink: /network-overlay
---

The repository for this project is private, but I'm happy to share it with potential employers if requested.

## About
For this project I constructed a logical network overlay over a distributed set of nodes (servers/clients), where minimum spanning trees of these nodes are constructed to route wire formats (packets) between them in a distributed system.

## Description
The Registry node holds the main server in which all MessagingNodes (clients) connect and register their ip addresses and port numbers. This is initiated by the following commands:

```java csx55.overlay.node.Registry <registry-port>``` starts a server in the specified port number.

```java csx55.overlay.node.MessagingNode <registry-host> <registry-port>``` connects a MessagingNode (client) to the Registry where it is then registered.

This is demonstrated in the following screenshot. The Registry node (bottom-right), named Tokyo, creates a server socket in port 5000, then each MessagingNode is connected to Tokyo's socket in port 5000 and registered:

(Click image to enlarge)
[![nodes-registered](/assets/img/all-nodes-registered.png)](/assets/img/all-nodes-registered.png)

Once all MessagingNodes have been registered, the Registry creates an overlay by linking each node to a specified number of other nodes. This specified number is determined by the ```setup-overlay``` command (explained [below](#server-commands)). Each link is bidirectional and is assigned a random weight between 1-10. Each MessagingNode creates its own minimum spanning tree out (MST) of the Registry's overlay.

After all MSTs are created, the MessagingNodes are ready to begin sending messages to each other for a specified number of rounds determined by the ```start``` command (explained [below](#server-commands)). For each round, a node sends 5 messages to another randomly selected node, where the payload of each message is a random integer. Nodes send/receive all data as encoded/decoded bytes in the form of wire formats (explained [below](#wire-formats)).

Once all rounds of messages have been completed, the server outputs a summary of summed up totals from each node that is used to verify that all rounds of messages have been correctly sent and received based on the following observations:

1. The total number of messages sent from each node must match. This is because all nodes must send 5 messages per round.
2. The cumulative sum of sent messages across the set of all nodes must match the cumulative sum of received messages across the set of all nodes. The number of messages received for each node differs since the receiving node is chosen at random.
3. The cumulative sum of sent payloads (integers) across the set of all nodes must match the cumulative sum of received payloads across the set of all nodes.

These are observed in the [Results](#results) below.

## Terminal Commands

Once all MessagingNodes are connected to the Registry (server) and are registered, the following terminal commands are used to determine the next steps:

#### Server commands:

```list-messaging-nodes``` : Lists the ip address and port number of each registered messaging node.

[![list-nodes](/assets/img/list-messaging-nodes.png)](/assets/img/list-messaging-nodes.png)

```setup-overlay <number-of-connections>``` : This command results in the Registry setting up the overlay. This is achieved by connecting each node to number-of-connections other nodes, then assigning a weight value to each link, which is a random number between 1-10 in this case. The Registry then sends wire fromats (packets) to each messaging node containing a list of peer nodes it should connect to.

[![setup-overlay](/assets/img/setup-overlay-ser.png)](/assets/img/setup-overlay-ser.png)

```list-weights``` : Lists each link in the overlay, comprised of its weight and 2 connected nodes.

[![weights](/assets/img/list-weights.png)](/assets/img/list-weights.png)

```send-overlay-link-weights``` : Tells the Registry to send wire fromats (packets) containing the link weights of the entire network overlay to all registered Messaging nodes in that overlay. This allows each messaging node to be aware of all the links in the system's network and uses this data to construct a minimum spanning tree (MST). Each node builds its own MST with itself as the root and spanning all other nodes in the network. This command is issued after the setup-overlay command.

[![send-weights](/assets/img/send-overlay-link-weights.png)](/assets/img/send-overlay-link-weights.png)

```start <number-of-rounds>``` : This results in nodes exchanging messages within the overlay. Each node sends 5 messages to another random node a total of number-of-rounds times. See [results](#results) below.

#### Client commands:

```print-mst``` : Prints the MessagingNode's minimum spanning tree, listed in Breadth-First Search order.

[![mst](/assets/img/print-mst.png)](/assets/img/print-mst.png)

```exit-overlay``` : The MessagingNode first sends a deregistration request (explained below) to the Registry then exists after it receives a confirmation from the Registry.

## Wire Formats

The following are all of the different wire formats (packets) sent and received between nodes:

REGISTER_REQUEST: Sent to the Registry (server) by a MessagingNode (client) upon connection. Contains a MessagingNode's ip address and port number.

REGISTER_RESPONSE: After a MessagingNode sends a request to register, the Registry sends back a response indicating success or failure of that particular node's registration.

DEREGISTER_REQUEST: Initiated by the ```exit-overlay``` command, this message is sent to the Registry by a MessagingNode so it may deregister and exit.

MESSAGING_NODES_LIST: Initiated by the ```setup-overlay``` command, this is a list of linked peer nodes sent to each MessagingNode by the Registry.

LINK_WEIGHTS: Initiated by the ```send-overlay-link-weights``` command, this is a list of all link weights of the network overlay sent to each MessagingNode by the Registry, after which each node creates its own minimum spanning tree.

TASK_INITIATE: Initiated by the ```start``` command, this is sent by the Registry to inform all MessagingNodes in the overlay to begin sending messages to each other.

MESSAGE: These are the messages which are sent from one MessagingNode to another following the ```start <number-of-rounds>``` command, where 5 messages are sent ```<number-of-rounds>``` times. Each packet includes the payload and its route to the destination MessagingNode. The payload is just a random integer. Each sender uses its own minimum spanning tree for the route to the receiving node.

TASK_COMPLETE: This is sent to the Registry by a MessagingNode that has completed its ```<number-of-rounds>```. It contains the MessagingNode's ip address and port number.

PULL_TRAFFIC_SUMMARY: This is sent to all registered nodes by the Registry once it receives all TASK_COMPLETE messages from them. This signals each MessagingNode to send over summaries of its traffic.

TRAFFIC_SUMMARY: Upon receiving the PULL_TRAFFIC_SUMMARY request from the Registry, each MessagingNode responds with a summary of its traffic which includes number of messages sent, received, and relayed. Since each MESSAGE's payload is an integer, the summary also includes a summation for sent payloads and a summation for payloads recieved.

## Results

The following is a screenshot of the output following the ```start 5000``` command. This results in each MessagingNode sending 5 messages to another random node 5000 times, which is a total of 25,000 messages sent by each node.

Each row represents a MessagingNode except for the last row. The columns, read from left to right, are the node's ip address, total messages sent, total messages received, sum of all sent integer payloads, sum of all received integer payloads, and total messages relayed.
The last row is the cumulative sum of each column (except for messages relayed), which is used to verify that all messages have been correctly sent and received across all MessagingNodes.

[![start](/assets/img/start-5000.png)](/assets/img/start-5000.png)

As shown in the output above, all three conditions for correctness, as previously described [here](#description), are satisfied.
1. The total number of messages sent is equal across all MessagingNodes at 25,000 sent messages each.
2. The cumulative sum of total sent messages across all MessagingNodes is equal to the cumulative sum of total received messages across all MessagingNodes, which is 250,000.
3. The cumulative sum of sent integer payloads across all MessagingNodes is equal to the cumulative sum of received integer payloads across all MessagingNodes, which is 921216498979.00.

This whole ```start``` process initially took minutes to complete, but I managed to bring it down to just a few seconds. I achieved this by adjusting how the sending and receiving of messages is synchronized in and between MessagingNodes.