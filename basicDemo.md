## Understand the Internet Protocol stack

### Prerequisites:

You need wireshark.  It's free and easy to download and install.

Quick start guide:
- Press the start button (blue shark fin) (or command + e on a Mac) to start packet capture
- If it asks you to select an interface, you probably want to select *Wi-Fi:en0*
- When you're done capturing, press the stop button (red square) (or command + e) again

### Demo 1:  `ping`
1. open a terminal window
2. find your own IP and MAC address.  your MAC address is part of the link layer communication between you and your router 
and is theoretically unique to your network adapter.  your IP address is how the rest of the interenet finds you.
  - type `ifconfig` and find the `en0` segment, or simply type `ifconfig en0`
  - your IP address is next to `inet` and your MAC address i snext to `ether`
    - my IP address today is 10.248.117.87
    - my MAC address always is f0:18:98:60:61:99
  - write them down, or leave this terminal window open for reference and use another for the steps below
3. start packet capture with wireshark
4. in the terminal window
  - type `ping www.google.com`
  - after 4 or 5 responses, escape from `ping` (`ctrl + c`)
5. stop packet capture with wireshark

You have a lot of packets in wireshark now.  The protocol column shows you things like ICMP, TCP, TLS, etc.
This is a lot of information.  We want to actually see what happened, so we need to filter.  `ping` uses the ICMP protocol:

6. In the filter box (just below the toolbar buttons) type `icmp` and press enter.

You should see a series of packets which are all the same color and protocol.  The source and desination addresses include your IP and some other IP.

Whose IP address is that?  You can probably make a good guess, but if you want to know for sure, you can use `dig`:

7. Perform a reverse DNS lookup (i.e., find the domain name for a given IP address):
- `dig -x [IP]` (`-x` is the reverse part of the DNS lookup)
- for 'dig -x 172.217.23.36` I see *lhr35s02-in-f36.1e100.net*.  What the heck is that?  I cheat and take that to WolframAlpha,
which tells me it belongs to Google.
- we could go the other way and do a DNS lookup with `dig www.google.com` (note, no `-x` flag).  I get 172.217.23.36 back, which confirms our answer.

Back to wireshark.  Now we know these ICMP packet are going back and forth between us and www.google.com, which is not surprising, since that's what we were hoping would happen with the `ping`.  

8. Select any frame, if not selected already.  In the bottom half of the wireshark window, you should see the collapsed packet summary.  Mine shows:
- *Frame2*
- *Ethernet II*
- *Internet Protocol Version 4*
- *Internet Control Message Protocol*

Hopefully this looks like a stack to you, with the lowest layer (the actual bits on the wire) on top and the highest layer protocol (ICMP) on the bottom (kind of upside down...).

9. Double click any frame and another window opens.  The top half should look like the bottom half of the main window.  The bottom half actually shows you the contents of the packet you selected in hex and (probably) ASCII (though by default it picks an encoding it thinks will be most readable based on the packet content).

10. In the new window, expand *Ethernet II*.  You should see part of the hex highlighted in the lower window.  This is the header for the Ethernet frame.  The datalink layer considers everything else in the packet to be payload, which shows the concept of **encapsulation** or **embedding**.  You should also see your MAC address directly encoded in the hex, which is nice.

You should see source and destination MAC addresses, and one of them should match yours.  The other MAC address belongs to the node at the other end of your current datalink layer link (most likely a router).  We'll dig into that later.  For now, it's sufficient to recognize that the datalink layer is associated with your local, shared media, multiple access network, so you're communicating with your local router, which can then send your packet off to www.google.com (or route a packet from www.google.com back to you).

The datalink layer used to represent multiple, shared access, so everyone could talk to everyone else at that layer.  You can think of the datalink layer as the devices physically conneted to one another, so you know where evyerone is and can talk to anyone to whom you're connected.  With wired Ethernet, this is actually true.  With wireless, it's mor eof an analogy.  Anyway, you're directly connected to your router.  You're not only not directly connected to www.google.com, but you're probably not connected to someone who is connected to www.google.com.  One step at a time, though.  We address an Ethernet frame to our router, with www.google.com's IP address, and hope the router can figure out how to go the rest of the way.

11. Expand *Internet Protocol Version 4*.  Again, you'll see part of the packet highlighted, which shows the IP header.  The network layer considers everything else in the packet to be payload.  If you decoded the hex, you would find that part of this header matched your IP address and the other part matched that for www.google.com.  The top part of the window does this decoding for you.

12. Finally, expand *Internet Control Message Protocol*.  This is the rest of the packet.  Some interesting things to note:  You can click through the relevant parts of the packet (e.g., *Type*, *Timestamp*) and see they only take up a few bytes.  What is the rest for?  It's mostly padding.  Ethernet frames have a minmum payload size to ensure CSMA/CD works.  The payload here has to be filled out to 48 bytes.

Fin.
