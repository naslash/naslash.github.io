---
layout: page
title: TCP/IP Network Overlay of Minimum Spanning Trees in Java
permalink: /network-overlay
---

The repository for this project is private, but I'm happy to share it with potential employers if requested.

## About
I constructed a logical network overlay for a distributed set of nodes (servers/clients). Each node builds a minimum spanning tree (MST) used to route messages encoded as wire format packets throughout the distributed system.

## Description
The Registry node hosts the main server to which all MessagingNodes (clients) connect to register their ip addresses and port numbers. This process is initiated with the following commands:

```java csx55.overlay.node.Registry <registry-port>``` starts a server on the specified port.

```java csx55.overlay.node.MessagingNode <registry-host> <registry-port>``` connects a MessagingNode (client) to the Registry, where it is registered.

This is illustrated in the following screenshot. The Registry node (bottom-right), named Tokyo, creates a server socket on port 5000, then each MessagingNode connects to Tokyo's server on port 5000 and registered:

(Click image to enlarge)
[![nodes-registered](/assets/img/all-nodes-registered.png)](/assets/img/all-nodes-registered.png)

Once all MessagingNodes have been registered, the Registry creates an overlay by linking each node to a specified number of other nodes. This specified number is determined by the ```setup-overlay``` command (explained [below](#server-commands)). Each link is bidirectional and is assigned a random weight between 1-10. Each MessagingNode constructs its own minimum spanning tree (MST) from the Registry's overlay.

After all MSTs have been constructed, the MessagingNodes are ready to begin sending messages to each other for a specified number of rounds, determined by the ```start``` command (explained [below](#server-commands)). During each round, every node sends five messages to a randomly selected destination node, where the payload of each message is a random integer. Nodes send and receive all data as encoded byte streams defined by the wire format protocol (explained [below](#wire-formats)).

Once all messaging rounds have completed, the server outputs summary statistics from each node which is used to verify that all rounds of messages have been correctly sent and received based on the following observations:

1. The total number of messages sent by each node must be identical. This is because each node must send 5 messages per round.
2. The cumulative sum of sent messages across the set of all nodes must match the cumulative sum of received messages across the set of all nodes. The number of messages received for each node differs since the receiving node is chosen at random.
3. The cumulative sum of payload values (integers) sent across all nodes must match the cumulative sum of received payloads across the set of all nodes.

These properties can be observed in the [Results](#results) section below.

## Terminal Commands

Once all MessagingNodes have connected to the Registry and completed registration, the following terminal commands determine the next steps:

#### Server commands:

```list-messaging-nodes``` : Lists the IP address and port number of each registered MessagingNode.

[![list-nodes](/assets/img/list-messaging-nodes.png)](/assets/img/list-messaging-nodes.png)

```setup-overlay <number-of-connections>``` : This command instructs the Registry to construct the network overlay. This is achieved by connecting each node to *number-of-connections* other nodes, then assigning each link a random weight between 1 and 10. The Registry then sends wire formats to each MessagingNode containing the list of peer nodes to which each MessagingNode should connect.

[![setup-overlay](/assets/img/setup-overlay-ser.png)](/assets/img/setup-overlay-ser.png)

```list-weights``` : Lists every link in the overlay, including its weight and the two connected nodes.

[![weights](/assets/img/list-weights.png)](/assets/img/list-weights.png)

```send-overlay-link-weights``` : Instructs the Registry to send wire formats containing the link weights of the entire network overlay to all registered MessagingNodes in that overlay. This allows each MessagingNode to learn every link in the overlay and use this data to construct a minimum spanning tree (MST). Each node builds its own MST rooted at itself and spanning every other node in the network. This command is typically issued after *setup-overlay*.

[![send-weights](/assets/img/send-overlay-link-weights.png)](/assets/img/send-overlay-link-weights.png)

```start <number-of-rounds>``` : This command initiates message exchange among all nodes in the overlay. Each node sends five messages to a randomly selected node for the specified number of rounds. See [results](#results) below.

#### Client commands:

```print-mst``` : Prints the MessagingNode's minimum spanning tree, listed in breadth-first search (BFS) order.

[![mst](/assets/img/print-mst.png)](/assets/img/print-mst.png)

```exit-overlay``` : The MessagingNode first sends a deregistration request (explained [below](#wire-formats)) to the Registry, then exits after receiving confirmation from the Registry.

## Wire Formats

The following wire formats (encoded byte streams) are sent and received between nodes:

**REGISTER_REQUEST:** Sent to the Registry by a MessagingNode upon connection. Contains a MessagingNode's IP address and port number.

**REGISTER_RESPONSE:** After a MessagingNode sends a request to register, the Registry sends back a response indicating success or failure of that particular node's registration.

**DEREGISTER_REQUEST:** Initiated by the ```exit-overlay``` command, this is sent to the Registry by a MessagingNode requesting deregistration before exiting the overlay.

**MESSAGING_NODES_LIST:** Initiated by the ```setup-overlay``` command, this is a list of linked peer nodes sent to each MessagingNode by the Registry.

**LINK_WEIGHTS:** Initiated by the ```send-overlay-link-weights``` command, this contains a list of every link in the network overlay and its associated weight. The Registry sends this information to each MessagingNode, after which each node constructs its own minimum spanning tree.

**TASK_INITIATE:** Initiated by the ```start``` command, this is sent by the Registry to inform all MessagingNodes in the overlay to begin sending messages to each other.

**MESSAGE:** These messages are sent from one MessagingNode to another following the ```start <number-of-rounds>``` command. Each node sends five messages during each round. Each message includes the payload and its route to the destination MessagingNode. The payload consists of a randomly generated integer. Each sender routes the message using its own minimum spanning tree (MST).

**TASK_COMPLETE:** This is sent to the Registry by a MessagingNode that has completed all assigned rounds. It contains the MessagingNode's IP address and port number.

**PULL_TRAFFIC_SUMMARY:** This is sent to all registered nodes by the Registry once it receives all TASK_COMPLETE messages from them. This requests that each MessagingNode send its traffic summary back to the Registry.

**TRAFFIC_SUMMARY:** Upon receiving the PULL_TRAFFIC_SUMMARY request from the Registry, each MessagingNode responds with a summary of its traffic, which includes the number of messages sent, received, and relayed. Since each MESSAGE's payload is an integer, the summary also includes the cumulative totals of sent and received payload values.

## Results

The following is a screenshot of the output following the ```start 5000``` command. This causes each MessagingNode to send five messages to a randomly selected node during each of the *5,000* rounds, for a total of 25,000 messages sent by each node.

Each row represents a MessagingNode except for the last row. The columns, read from left to right, are the node's IP address, total messages sent, total messages received, sum of all sent integer payloads, sum of all received integer payloads, and total messages relayed.
The final row contains the cumulative totals for each column (except for messages relayed), which is used to verify that all messages have been correctly sent and received across all MessagingNodes.

[![start](/assets/img/start-5000.png)](/assets/img/start-5000.png)

As shown in the output above, all three conditions for correctness, as previously described [here](#description), are satisfied.
1. The total number of messages sent is equal across all MessagingNodes at 25,000 sent messages each.
2. The cumulative sum of total sent messages across all MessagingNodes is equal to the cumulative sum of total received messages across all MessagingNodes, which is 250,000.
3. The cumulative sum of sent integer payloads across all MessagingNodes is equal to the cumulative sum of received integer payloads across all MessagingNodes, which is 921,216,498,979.00.

## Optimization

The whole ```start``` process initially took minutes to complete, but I managed to bring it down to just a few seconds by redesigning the synchronization between message sending and receiving in the MessagingNode and Registry classes. Previously, I ```synchronized``` more code than necessary, forcing other operations to wait for locks to be released or for the state updates required before they could begin executing. I refactored the implementation so that only the critical sections of each task are synchronized.

The process consists of four separate tasks that involve sending and/or receiving [wire formats](#wire-formats) throughout the overlay. Because these operations require synchronization, I defined a dedicated ```synchronized``` method for each one. This allows the operations to execute independently while still maintaining thread safety. These methods are:

- In MessagingNode, ```synchronized void sendMessages(String[] route)``` sends five messages to the next node in the specified route, where they are then relayed to their destination at the end of the route. A destination node is chosen at random from a HashMap that holds the route to each node in the minimum spanning tree.
- In MessagingNode, ```synchronized void processPayload(String[] route, int payload)``` handles incoming payloads by either relaying them to the next node in the route, or adding the payload integer value to the receiving node's cumulative total if the current node is the destination.
- In the Registry, ```synchronized void nodeTaskComplete()``` processes the received TASK_COMPLETE [wire formats](#wire-formats) sent by each MessagingNode after completing their last round of messages. Once every MessagingNode has reported completion, this method first calls ```sleep()``` on the thread for 500 milliseconds to ensure all payloads have reached their destinations, and then sends a PULL_TRAFFIC_SUMMARY [wire format](#wire-formats) to request a traffic summary from each MessagingNode.
- In the Registry, ```synchronized void addTrafficSummary(String nodeID)``` processes the received TRAFFIC_SUMMARY [wire formats](#wire-formats) sent by each MessagingNode, and after processing the last one, it prints a summary report of all nodes as shown [above](#results).

Once a TASK_INITIATE wire format is received by a MessagingNode, ```sendPayload()``` (screenshot below) is called to execute the message sending process, which is where the synchronized ```sendMessages(String[] route)``` method is called. After completing the final round, a TASK_COMPLETE [wire format](#wire-formats) is sent to the Registry, which executes its synchronized ```nodeTaskComplete()```.

[![send](/assets/img/send-payload.png)](/assets/img/send-payload.png)

Payloads are transmitted using the MESSAGE [wire format](#wire-formats).

Any time a MessagingNode receives a payload, ```receivePayload()``` (screenshot below) is called to decode the incoming MESSAGE wire format and pass its data to the synchronized ```processPayload()``` method.

[![receive](/assets/img/receive-payload.png)](/assets/img/receive-payload.png)

This design allows a MessagingNode to continuously send messages while also handling incoming payloads, without the two operations overlapping thanks to Java's synchronization mechanism.