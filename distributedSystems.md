## 1 - Processes and Communication

Differentiate between the following:
- Persistent and transient communication
In persistent communication, a message is stored on the server as long as it takes to deliver it to the reciever.
In transient communication, a message is discarded by the server if it cannot be delivered.

- Synchrounous and asynchronous communication
**synchronous communication** - the sender will be blocked until the message is recieved or at least stored by the reciever.
**asynchronous communication** - the sender will continue immidiately after the message is sent on its way.

- Concurrent and iterative servers
**iterative** servers will only serve one client at a time.
**concurrent** servers will serve many clients at once.

- Stateful and stateless servers
A **stateful** server will store some of the clients info between requests. This is also called a **session**.
A **stateless** server does not store any information about the requests beeing made. Every request is a sort of *clean sheet*. 

- Multithreaded clients and multithreaded servers
In a multithreaded server, a request will spawn a new thread assigned to respond to the request.
In a multithreaded client, wanting to send a request will spawn a new thread in the client, this thread will send the request and wait for the reply.

## 2 - Naming, Replication and Fault Tolerance
1. Compute the finger table for each replica

**4**
- 1 -> 13
- 2 -> 13
- 3 -> 13
- 4 -> 13
- 5 -> 21

**13**
- 1 -> 21
- 2 -> 21
- 3 -> 21
- 4 -> 26
- 5 -> 4

**21**
- 1 -> 26
- 2 -> 26
- 3 -> 26
- 4 -> 4
- 5 -> 4

**26**
- 1 -> 4
- 2 -> 4
- 3 -> 4
- 4 -> 4
- 5 -> 13


2. What is the address size for this ring?
There are 32 nodes in this ring. 2 to power of 5 is 32, so the address size is 5 bits.

3. Resolve key = 10 from server replica 26 (show your steps)
The next step from *26* is *4*, this is because 4 < 10 < 13. From *4* we go to *13*. This is the Node responsible for *10*.

4. Resolve key = 5 from server replica 4 (show your steps)
From *4* you go to *13*, and this is the node responsible for *5*.

5. Which server replicas are responsible for the keys = 0, 4, 15, 16, 24, 26 and 30? (show the formula used)
0, 4, 30 -> 4
15, 16 -> 21
24, 26 -> 26

## 3 - Communication
1. What is the delay in the network when a message is multicast from C to D?
C -> Rc -> Rb -> B -> Rb -> Ra -> A -> Ra -> Re -> Rd -> D
1 - 25 - 1 - 1 - 25 - 1 - 1 - 80 - 30 - 1 = 166
2. What is the delay in the network when a message is multicast from C to E?
C -> Rc -> Rb -> B -> Rb -> Ra -> A -> Ra -> Re -> Rd -> D -> Rd -> Re -> E
1 - 25 - 1 - 1 - 25 - 1 - 1 - 80 - 30 - 1  - 1 - 30 - 1= 198 
3. Compute the stretch/relative delay penalty (RDP) when a message is multicast from C to E provided that the best path (least cost) will be used by the underlying physical routers to route the message from C to E

The overlay delay was 198.
The underlying path would be:
C -> Rc -> Rd -> Re -> E
with a total delay of 42.
RDP is then 198/42 = 4.7

4. If the tree is modified such that path A -> D is removed and a new path A -> E is created resulting in a new path for the tree as: C -> B -> A -> E -> D. Compute the relative delay penalty to route a message from C to E.

**overlay** delay:
1 + 25 + 2 + 25 + 2 + 80 + 1 = 136

**underlay** delay:
1 + 10 + 30 + 1 = 42

**RDP** is 136/42 = 3.2

5. Based on your calculations in (3) and (4), which tree would be nore efficient to route a message from C to E.

## 4 - Replication and Consistency
1. Describe the benefits and challanges of replication in a distributed system
**Benefits**
It will increase scalability and availability. Imagine a company having all their data located on one single server in London. If many people need the data at once, they will all have to go through this one server, which may cause delays.
And if someone are located far away from London, there will always be a delay due to the distance the requests and responds will have to travel.
Having only one one copy of the data is also very insecure. If you lose your one copy, all is lost. So replication adds security.
Scalability can also be increased by not replicating all of the data, but the parts being used the most. This data can be stored more easily accessible, in a cache system.

Having many replicas also introduce many problems. For instance, if the data is updated, all of the replicas need to be updated as fast as possible. This will consume a lot of network bandwidth if there are a large number of replicas. And it may also be time consuming.
There can also arise many problems if the data is not kept properly in sync, meaning at some point not all replicas are exactly the same anymore.
To ensure that all replicas are kept consistent, one migh introduce some sort of main storage that is kept up-to-date, and all replicas make sure they match that main storage, and when they recieve changes themselves, they update the main storage.
This will however create a bottleneck in the main storage, and read-write conflicts may occur as many different replicas access the main storage at once.

2. Differentiate between Remote-write protocols and Local-write protocols
### Remote-write
- All write operations are forwarded to the primary replica
- Primary performs the update on its local copy and forwards the update to backup servers
- Backup server performs the update and sends acknowledgements to the primary
- Primary sends ack to initial process
- Read operations are carried out locally at each replica

**Remote-Write provides**
- A simple way to implement sequential consistency
- Guarantees that client see the most recent write operations

**However, communication overhead is high in Remote-Write Protocols**
- Client blocks until all the replicas are updated

**Remote-Write Protocols are applied to distributed systems that require fault-tolerance**
- Replicas are mostly placed on the same LAN to reduce latency
- Non-blocking approach is possible - not fault tolerant and read of most recent write is not guaranteed

### Local-write
- All write operations require the primary copy to first be migrated to local replica server
- Read operations are carried out locally at each replica

**Local-Write**
- Can provide sequential consistency

**Main issue: where is the data item right now?**
- More time can be used by processes to locate the primary data item first

**Advantage**
- Multiple successive wirte operations can be carried out locally while reading simultaneously
- Possible only if non-blocking protocol is used - updates are propagated to replicas after finishing with local updates

## 5 - Coordination
1. Differentiate between lamport clock and vector clock
### Lamport clock
- A process increments its counter before each event in that process
- When a process sends a message, it includes its counter value with the message
- On receiving a message, the receiver process sets its counter to be greater than the maximum of its own value and the received value before it considers the message received

### Vector clock
- Initially all clocks are zero
- Each time a process experiences an internal event, it increments its own logical clock in the vector by one
- Each time a process prepares to send a message, its sends its entire vector along with the message being sent
- Each time a process receives a message, it increments its own logical clock in the vector by one and updates each element in its vector by taking the maximum of the value in its own vector clock and the value in the vector in the received message (for every element)

### Lamport clock
- A process increments its counter before each event in that process
- When a process sends a message, it includes its counter value with the message
-On receiving a message, the receiver process sets its counter to be greater than the maximum of its own value and the received value before it considers the message received

### Vector clock
- Initially all clocks are zero
- Each time a process experiences an internal event, it increments its own logical clock in the vector by one
- Each time a process prepares to send a message, its sends its entire vector along with the message being sent
- Each time a process receives a message, it increments its own logical clock in the vector by one and updates each element in its vector by taking the maximum of the value in its own vector clock and the value in the vector in the received message (for every element)

### Lamport clock
- A process increments its counter before each event in that process
- When a process sends a message, it includes its counter value with the message
-On receiving a message, the receiver process sets its counter to be greater than the maximum of its own value and the received value before it considers the message received

### Vector clock
- Initially all clocks are zero
- Each time a process experiences an internal event, it increments its own logical clock in the vector by one
- Each time a process prepares to send a message, its sends its entire vector along with the message being sent
- Each time a process receives a message, it increments its own logical clock in the vector by one and updates each element in its vector by taking the maximum of the value in its own vector clock and the value in the vector in the received message (for every element)

2. Discuss how distributed algorithm works in the mutual exclusion algorithm
All processes keep a logical clock. If a process wants to access the mutex area, it will multicast this intent along with its logical clock to all other processes. 
The other processes will consider this message.
If a process is not using the mutex area, it will reply OK.
If it it using the area, it will not reply until it is done.
If it also wants to use the area, they will compare logical clocks and the one with the lowest value will get access first.

3. Discuss how centralized algorithm works in mutual exclusion algorithm
In the centralized algorithm, one process is set to be the coordinator. All requests will go through the coordinator. This way the coordinator will always know if the mutex area is availible or not.

4. Discuss how token-ring algorithm works in mutual exclusion algorithm
All the processes are organized in a logical ring. There is one token, and this token is passed along the ring. To enter the critical section, you will need to have the token. If you get the token and do not need to use the critical section, you just pass along the token to the next process.

5. Explain briefly how bully election algorithm works
n the bully algorithm, the process with the highest id is the coordinator. When some process notices that the current coordinator does not respond to requests, it will issue an ELECTION message to all processess with a higher id. This is repeated until no process responds to the ELECTION message. This must then be the highest process, and it will become the new coordinator.

## 6 Fault-Tolerance
1. Explain how process resilience can be achieved in a distributed system
This can be done by having several identical processes in a group. This way, if one process fails, another can will do its job.

2. Differentiate between flat process groups and hierarchial process groups, and discuss their benefits and drawbacks
In a **flat group**, all processess are treated equally. This means that there is no single point of failure. The main problem is that decision making is complicated. This will also induce delays.
In a **hierarchial group**, there will be a coordinator. This means that decisions are fast and easy to make, but it will also have a single point of failure.

3. Explain feedback implosion and how to use nonhierarchial feedback control to mitigate it
A process will send out a NACK regarding some missing message. This NACK is sent out to all group members, and if some process also wants to send out a NACK regarding the same message, this NACK will not be sent. This way the sender only receives one NACK.

4. Discuss briefly the five classes of failures that can occur in RPC systems
- The client cannot locate the server
- The request message from the client to the server is lost
- The reply message from the server to the client is lost
- The server crashes after receiving a request
- The client crashes after sending a request

## 7 Security
Discuss the cryptography schemes that can be used to provide confidentiality, integrity, authentication, and non-repudiation

### Symmetric Key Cryptography
This is where all sides share the same key. Meaning that if Bill and Bob want to send encrypted message to one another, they first agree on a key. They both use this key to encrypt and decrypt all messages that they send.

Some popular symmetric key encryption systems include:
- DES - Data Encryption Standard
- Blowfish
- RC5
- AES - Advanced Encryption Standard

There are som issues with symmetric key cryptography. The main one is perhaps that the two parties must agree on the secret key in advance.

It is also not very scalable since user need many pairs of unique keys.

How does one deliver a key to the other party securely?
It does not provide non-repudiation.

### Public Key Cryptography
In this approach, the sender and reciever do not share a secret key. They do however have public keys that are availible to anyone. This key can be used to encrypt a message and send it to its user. The user can then use its private key to decrypt the message.

This solves the issue of exchaning keys, as the public key is all that is need to encrypt a message, but the same key can not be used to decrypt the message.

One can however derive a private key using the public key, but this is made very computationally difficult. Some scheemes used to calculate private-public key pairs include:
- Integer-Factorization Schemes, difficult to factor large integers (RSA)
- Discrete Logarithm Schemes
- Elliptic Curve Schemes

### Hash Functions
A hash function will reduce a message of arbitrary length into a fixed-length output.

Hash functions are not means of encrytion, as they are one-way. They also do not have any keys.

Some popular hash functions include:
- MD4 (128 bits)
- MD5 (128 bits)
- MD6 (up to 512 bits)
- SHA-1 (160 bits)
- SHA-2 (SHA-256 and SHA-512 b)
- SHA-3
- RIPEMD-160

A hash function should ideally be fast. It must be collision resistant, meaning two different messages should not produce equal hashes. It should be one-way, meaning one cannot derive the original message from the hash. You should also not be able to modify a message without modifying the hash.

### SSL
Secure sockets layer provides transport layer security to any TCP-based application using SSL services.

TLS(Transport Layer Security) is an updated and more secure version of SSL.

In this protocol, when a user wants to make a secure connection to a server. It will send a TLS-hello request, meaning that the user is asking the server to establish a secure connection. 
The server will respond with a public key. The user will use this public key to encrypt a message containing a key that can be used for symmetric encryption.
The server will decrypt this message using its private key, and store the key sent by the user. Both parties now have a identical key that can be used for symmetrical encryption.

In TLS the server will also respond to the TLS-hello request with a TLS-certificate. This certificate will confirm the identity of the server. These certificates are distributed and verified by third-parties.

## 8 - Cloud Computing
Explain the following cloud service models:
- Infrastructure as a service
- Platform as a service
- Software as a service

### Infrastructure as a service
This is where a vendor provides computing resources to the consumer. Examples of such services includes:
- Amazon Elastic Compute (EC2)
- Google Compute Engine
- Azure VM

This kind of service can provide the compute capabilities when you need them without any upfront investment. It can also quickly scale to your needs.

### Platform as a service
This is a cloud service that provides a platform that the consumer can use to build and deploy software.

It is a model in whick a third-party provider hosts application development platforms and tools on its own infrastructure and makes them availible to customers over the internet.

Examples include:
- AWS Elastic Beanstalk
- Google App engine
- Heroku

### Software as a service
A software distribution model in which a third-party provider hosts applications and makes them availible to customers over the internet.

Examples:
- Google Docs
- Netsuite



