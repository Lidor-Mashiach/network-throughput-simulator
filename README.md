# 📡 Network Throughput Simulator

A Python-based simulation framework designed to evaluate and compare TCP and UDP performance under varying file sizes and connection loads.  
The project consists of a **client-server model**, where the client requests a file of configurable size, and the server transmits it via **multiple parallel TCP/UDP connections**.

The goal is to simulate a realistic network load and **analyze throughput, speed, and reliability** for each transport protocol.

---
## 🧠 Project Structure & Key Components

The simulator is composed of three primary Python files:

- `Client_Side.py`  
  The main client application.  
  - Listens for server offers via UDP broadcast (using Scapy sniffing).
  - Accepts user input for file size and number of connections.
  - Initiates multiple concurrent TCP and UDP transfers.
  - Tracks throughput, time, and success rate for each connection.

- `Server_Side.py`  
  The backend server.
  - Continuously broadcasts its presence using UDP offer packets.
  - Listens for client requests via both TCP and UDP.
  - Sends the requested file by splitting it into payload segments.
  - Uses multithreading to handle simultaneous client requests.

- `UDP_Packet.py`  
  A modular utility for encoding and decoding custom UDP messages.
  - Supports three message types:
    - `OFFER` (0x2) – server availability announcement
    - `REQUEST` (0x3) – client request with file size
    - `PAYLOAD` (0x4) – file segments sent from server to client
  - Automatically handles byte padding, segment IDs, and type-specific structure.

Each module follows clear separation of concerns and is well-documented with inline explanations.

## 🔄 How It Works – Protocol Flow

1. **Broadcast Discovery (UDP)**  
   - The server broadcasts an "offer" every second on a predefined port.
   - The client uses `scapy.sniff()` to capture these offers.
   - When a valid offer is received, the client initiates a connection.

2. **Client Configuration Input**  
   - The user is prompted to enter:
     - File size (in units like KB, MB, Gb, etc.)
     - Number of TCP connections to open
     - Number of UDP connections to open

3. **File Transfer Begins**  
   - For each TCP connection:
     - A reliable stream is established.
     - The file is sent in chunks until fully received.
     - Total speed and duration are measured.
     
   - For each UDP connection:
     - A single request packet is sent.
     - The server replies with multiple payload packets.
     - The client calculates the total segments received, speed, and success rate.

4. **Performance Output**  
   - The client prints a breakdown for each connection:
     - Total time
     - Total speed (bits/sec)
     - Percentage of successful packet receipt (UDP only)

---
## ▶️ How to Run

> ⚠️ The client and server should run on **different machines** (or VMs) on the same local network.

### 🖥️ Server Side

1. Run the server:
```bash
  python Server_Side.py
```
2. The server will:
   - Automatically detect its IP address.
   - Start broadcasting offers to the network.
   - Accept incoming TCP and UDP client connections on port `64000`.
   - Handle multiple connections concurrently using threads.

### 💻 Client Side

1. Run the client:
```bash
  python Client_Side.py
```
2. Enter the following inputs when prompted:
   - **File size** (e.g., `10MB`, `5Gb`, `2048Kb`)
   - **Number of TCP connections** (e.g., `2`)
   - **Number of UDP connections** (e.g., `3`)


3. The client will:
   - Listen for a broadcast.
   - Connect to the detected server.
   - Start transferring the file concurrently over all requested connections.

#### 🧪 Example Input

```text
Please enter the File Size.
Supported units are: b, Kb, Mb, Gb, B, KB, MB, GB.
> 5MB

Please enter the amount of TCP Connections -
> 2

Please enter the amount of UDP Connections -
> 3
```

#### 📤 Client Output Example

```text
####################################################################################
        Lidor's Team (Client) Started, listening for offer requests...
####################################################################################


Received offer from server 'Lidor's Team (Server)' at IP 192.168.1.20 and port 64000



###############################################################################
                    TCP transfer #1 finished
                    Total time: 2.74 seconds
                    Total speed: 14619854.45 bits/sec
###############################################################################


###############################################################################
                    TCP transfer #2 finished
                    Total time: 2.73 seconds
                    Total speed: 14699230.67 bits/sec
###############################################################################


Receiving from 192.168.1.20: Progress 1/5 segments
Receiving from 192.168.1.20: Progress 2/5 segments
Receiving from 192.168.1.20: Progress 3/5 segments
Receiving from 192.168.1.20: Progress 4/5 segments
Receiving from 192.168.1.20: Progress 5/5 segments


###############################################################################
                    UDP transfer #1 finished
                    Total time: 1.08 seconds
                    Total speed: 3949425.47 bits/sec
                    Percentage Of Packets Received Successfully: 100.00%
###############################################################################


###############################################################################
                    UDP transfer #2 finished
                    Total time: 1.05 seconds
                    Total speed: 4083214.29 bits/sec
                    Percentage Of Packets Received Successfully: 100.00%
###############################################################################


###############################################################################
                    UDP transfer #3 finished
                    Total time: 1.12 seconds
                    Total speed: 3825247.32 bits/sec
                    Percentage Of Packets Received Successfully: 100.00%
###############################################################################
```
