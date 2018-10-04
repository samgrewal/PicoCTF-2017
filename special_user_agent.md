This was another Level 1 problem. Here’s the prompt:

> We can get into the Administrator's computer with a browser exploit. But first, we need to figure out what browser they're using. Perhaps this information is located in a network packet capture we took: > data.pcap. Enter the browser and version as "BrowserName BrowserVersion". NOTE: We're just looking for up to 3 levels of subversions for the browser version (ie. Version 1.2.3 for Version 1.2.3.4) and ignore > any 0th subversions (ie. 1.2 for 1.2.0)
>
> HINTS:
> Where can we find information on the browser in networking data? Maybe try reading up on user-agent strings.

At first glance, this problem seemed like it was going to be exciting, because I enjoyed the previous one that dealt with packet capture data. Besides, who doesn’t hasn’t thought about about hacking into their administrator’s computer? 

I had heard the term ‘user agent’ before, and had a vaguely familiar idea of what it was, but I decided to follow the link in the hint to learn more. The website in the link explains what user agent strings are, and how they provide insight on your web browser name, operating system, device type and lots of other useful bits of information. I decided to analyze my own web browser’s user agent string first: 

```
	Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36
```

The website revealed, among other things, that I was running Chrome version 69.0.3497.100. Judging by the prompt, this was exactly the kind of information we were looking to find. The website revealed another valuable clue, that user agent strings are located in HTTP headers titled ‘User Agent’. 

Armed with this knowledge, I decided to comb through data.pcap in Wireshark and pay close attention to the HTTP packets. 

A few HTTP packets actually contained user agent strings, as follows:

```
	Wget/1.16 (linux-gnu)
```

However, prior knowledge and a google search confirmed that wget is a command line tool used to download files over HTTP, HTTPS, and FTP. It is not an actual web browser. 

Toward the middle of the data there was an HTTP GET packet with the following user agent string: 

```
	Mozilla/5.0 (X11; OpenBSD i386) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/36.0.1985.125 Safari/537.36
```

Based on the previous analysis of my browser’s user agent string and the instructions, the flag turned out to be Chrome 36.0.1985. I enjoyed this problem because it gave me another chance to play around in Wireshark, and because it taught me more about a concept I was always a little shaky on. 

