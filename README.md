# Method

I use a sliding window. The window has a minimum size of 2 packets, and slowly grows with the number of packets, but takes a penalty for every packet that takes longer than 2xRTT. RTT is computed on a moving average, with a learning rate of 0.1. This means that the RTT on a received packet is 0.9*RTT + 0.1*(current time - packet timestamp). Jitter is handled by acking every packet, and the sender keeps tracks of acks not received and resends those packets first. The sender then forgets about a packet if it receives an ack for it. The sender also keeps track of the packets received, and waits to print packets until it has continuous packets to print. Finally, the valility of a packet is verified using sha1 checksums. If the checksum is incorrect or malformed(not valid json, or doesnt contain a checksum), the packet is dropped. The chance of a sha1 collision is extremely low(1/2^256), so the chance of an error not being detected is very very low. 

# Pitfals

A current pitfal of my implementation is that its hard for my window to grow quickly, and if the throughput shrinks rapidly, the algorithm may overcompensate on the backoff and take a very long time(10k+) packets to catch back up. That being said, none of the tests address this circumstance.
