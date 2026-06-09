# Thread Bandwidth and Packet sizes

Source: Google Gemini

Thread is a low-power, IPv6-based mesh networking protocol designed primarily for the Internet of Things. Because it operates on the IEEE 802.15.4 standard, it is optimized for small, infrequent data packets rather than high-bandwidth data transfers, prioritizing battery life and mesh reliability.

## Network Bandwidth

- Physical Layer Rate: Thread operates in the 2.4 GHz ISM band with a maximum raw over-the-air data rate of 250 kbps.
- Effective Throughput: Due to overhead (such as MAC headers, security encryption, and acknowledgments) and the CSMA/CA channel access method, the practical application-level bandwidth is typically closer to 125 kbps.
- Multi-hop Impact: In a mesh network, packets often hop from router to router. 

Each hop consumes additional channel time, meaning multi-hop throughput is lower and varies dynamically with the number of hops (e.g., from source to destination).

## Packet Sizes (MTU and Payload)

- IEEE 802.15.4 Frame Limit: At the physical and MAC layers, the absolute maximum packet size is 127 bytes. After accounting for MAC headers and AES security, the actual usable payload space for an over-the-air frame is usually between 81 and 102 bytes.
- IPv6 Requirement: Standard IPv6 requires a minimum MTU (Maximum Transmission Unit) of 1280 bytes.
- 6LoWPAN Adaptation: To bridge the gap between 127-byte over-the-air frames and 1280-byte IPv6 packets, Thread uses 6LoWPAN. This adaptation layer handles IP packet fragmentation and robust header compression.

Because of these bandwidth and packet size constraints, Thread is not suited for streaming video or high-bandwidth tasks, but it is highly efficient for control signals, sensor telemetry, and automation commands (e.g., smart home environments using Matter-over-Thread).
