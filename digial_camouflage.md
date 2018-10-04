This was another Level 1 problem. Here’s the prompt:

> We need to gain access to some routers. Let's try and see if we can find the
password in the captured network data: data.pcap.
>
> HINTS:
> It looks like someone logged in with their password earlier. Where would log in
data be located in a network capture?
> If you think you found the flag, but it doesn't work, consider that the data
may be encrypted.

I got excited about this problem as soon as I read the prompt. Specifically, the indicators that we would be working with routers and captured network data. Finally, some real cybersecurity stuff!

With some quick googling, I figured out that data.pcap is a Packet Capture file, and is commonly analyzed with Wireshark. I had used Wireshark before in High School, but navigating it seemed as daunting back then as it did when I used it for this problem.

So, I went ahead and opened data.pcap in Wireshark. Gathering from my knowledge from CSE 3461, I
realized what lay before me was a sequence of packets from various OSI levels 2 and 3 protocols like
TCP, UDP, ICMP, HTTP, etc. It seemed like information overflow at first, but I had fun spending time
looking at the details of various packets and trying to mentally reconstruct the sequence of events the
data represented.

A hunch told me that the information we were looking for was probably in an HTTP packet. Looking back
to CSE 3901, I had learned that the HTTP protocol conveyed more realizable ‘content’ than any of the
others I saw. Furthermore, knowing that we were looking for a user’s password, I could assume that they
were filling out some sort of password form and submitting it. Knowing the 5 basic HTTP requests (GET,
POST, PUT, DELETE, and PATCH), I decided to look for user-input oriented requests like POST or PUT.
Scrolling through the data, I saw an HTTP GET, as well as an HTTP continuation packet. However,
located not too far from the start of the data, I saw an HTTP POST. Deciding to investigate, I clicked on it and saw a clue in the packet’s body, the string:

```
	userid=mathewsr&pswrd=aHJLUVNTTFd2Rw%3D%3D
```

I tried copy and pasting the raw password string into the Pico flag prompt, but it turns out this was not the flag.

This stumped me for a while, until another hunch suggested that the password was probably encoded. I
tried various online substitution cypher-solvers on the internet to try and crack the password, but that didn’t get anywhere. Then I realized the obvious, that the entire packet was probably encoded, because it definitely couldn’t be sending data in raw UTF-8 format.

Another google search revealed that HTTP packets are encoded using base64. After running the raw
password through a base64 decoder, I got the flag:

```
	hrKQSSLWvG
```

This problem was frustrating at a couple points, but fairly straightforward in retrospect. I enjoyed getting the chance to play around in Wireshark, and I particularly enjoyed the forensic flavor this problem had.
