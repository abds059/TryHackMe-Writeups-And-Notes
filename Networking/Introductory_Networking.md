# Introductory Networking

## Task 2 - OSI Model

- A standardised model used to demonstrate the theory behind computer networking
- Consist of 7 layers

### Layer 7 - Application Layer

- Provides
    - Networking options to programs running on a computer
    - Interface to applications in order to transmit data

- Data provided to application layer is passed down to the presentation layer

### Layer 6 - Presentation Layer

- Data is not usually in standardised format (Format that can be understood by Application Layer)
- Presentation Layer converts data into standardised format
- Also handles any encryption, compression or any other tranformations to data   
- Passes the data onto session layer

### Layer 5 - Session Layer

- Session layer receives formatted data from presenatation layer
- Tries to form a connection (session) with other hosts on the network
    - If a session cannot be created process goes no further
    - If a session is created the session layer
        - Maintains the session
        - Cooperate with the session layer of the remote computer to synchronise comunication

- Session created is unique for each communication allowing multiple request to different endpoints without data mixups (Opening two tabs in the browser simultaneously)

- When a successful connection has been logged between the host and remote computer data is passed down to Transport Layer

### Layer 4 - Transport Layer

- Choses the protocol over which data is to be transmitted 
- Most common protocols are TCP and UDP 

- #### TCP (Transmission Control Protocol)
    - Connection based protocol - Connection has to be established for the duration of the request
    - Reliable
    - Lost packets are resent
    - Used when accuracy is important (For e.g, file transfer or loading a page)

- #### UDP (User Datagram Protocol)

    - Packets of data are essentially thrown at the receiving computer
    - Used when speed is important (For e.g, video streaming)


- When protocol is selected the transport layer divides the transmission into bits sized pieces which makes it easier to transmit message successfully 
- These bit sized pieces are called segments in TCP while in UDP they are called datagram

### Layer 3 - Network Layer

- Responsible for locating the destination of your request through logical addressing (IP Address)
- For e.g,  When you want to request information from a webpage, the network layer takes the IP address for the page and determines the best route to take
- Logical addresses provide order to networks, categorising them and allowing us to properly sort them
- Most common form of logical addressing is the IPV4 format (for e.g, 192.168.1.1 - a common address for a home router) 

### Layer 2 - Datalink Layer

- Focuses on physical addressing of the transmission
- Receives packet from network layer (including IP address of the remote computer) and adds the physical (MAC) address of the receiving endpoint
- When information is sent across a network, the physical address that is used to identify where exactly to send the information
- Data Link layer presents the data in a format suitable for transmission
- Also serves an important function to make sure that received data hasn't been corrupted during transmission, which could well happen when transmitted by layer 1: the physical layer

- ##### MAC Address
    - Every network enabled device has a NIC (Network Interface Card) which comes with a unique address burnt onto it by the manufacturer called the MAC address

### Layer 1 - Physical layer

- Right down to the hardware level
- Here electrical pulses that make up data data transfer over a network are sent and received
- The physical layer    
    - Convert binary data transmission into signals and transmit them across the network
    - Receive incoming signals and convert them into binary data

### For the "Which Layer" Questions below, answer using the layer number (1-7)

- Which layer would choose to send data over TCP or UDP?
    - 4

- Which layer checks received information to make sure that it hasn't been corrupted ?
    - 2

- In which layer would data be formatted in preparation for transmission?
    - 2

- Which layer transmits and receives data?
    - 1

- Which layer encrypts, compresses, or otherwise transforms the initial data to give it a standardised format?
    - 6

- Which layer tracks communications between the host and receiving computers?
    - 5

- Which layer accepts communication requests from applications? 
    - 7

- Which layer handles logical addressing?
    - 3

- When sending data over TCP, what would you call the "bite-sized" pieces of data?
    - Segments

- **[Research]** Which layer would the FTP protocol communicate with?
    - 7

- Which transport layer protocol would be best suited to transmit a live video? 
    - UDP

---

## Task 3 - Encapsulation

- 
