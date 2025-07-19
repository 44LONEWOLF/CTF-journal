This challenge wasn't clear at first so I had to watch YouTube guide right away. But I still learned lots of stuff.

So basically, what you have to do in this challenge is use ```Wireshark``` to analyze the ```.pcap``` file.

What you gotta do is download Wireshark from [ https://www.wireshark.org/download.html ]

After you download it I recommend you to watch this video [ https://youtu.be/O7t0TPDCIoI?si=DhU4ywTn3RyJ6Gv1 ]. I would walk you through the process but it's better if you see it visually

I have spent hours to do this using tshark(a CLI version of Wireshark) and it gave me a hard time but I did it Nevertheless.

So now, I'll just talk about the CLI version of the solution. Here are the steps to deal with it in CLI(You will learn about a lot of stuff like tshark, sort, awk, xxd, strings, tr, base64......)

1. ``` tshark -r myNetworkTraffic.pcap -Y "tcp.payload" -T fields -e frame.time_epoch -e tcp.payload > payloads.txt ``` (tshark initiate the tool, 
    (-r) read the .pcap file, (-Y) display filter ("tcp.payload" tells tshark to only show packets that contain TCP payloads), 
    (-T) the output format (it means it only shows the specific fields you ask for), (-e frame.time = I want the timestamp column) 
    (-e tcp.payload = I want the payload column), (> payloads.txt) It will store the data in payloads.txt)

2. ``` sort -n payloads.txt > sorted_payloads.txt ``` (sort -n sorts numerically by timestamp)

3. ``` awk '{print $NF}' sorted_payloads.txt > time_only_payloads.txt ``` ((awk '{print $NF}')this will only choose the last field. 
    $NF means last field. And then store it in time_only_payloads.txt)

4. ``` xxd -r -p time_only_payloads.txt | tr -d '\n' | base64 -d > flag.txt ``` ((xxd) converts between hex and binary/ASCII, (-r) reverse mode: It means it tells xxd to convert hex → binary, instead of the default binary → hex.
 (-p) plain mode: It tells xxd to expect raw hex characters only, (tr -d '\n') deletes newline character, (base64 -d) decode base64 into ASCII or binary)

5. ``` strings flag.txt | grep 'picoCTF' ``` (This will show the flag)

### This is how I did it (with AI, of course). It teaches me many things(including how to deal with frustration) and, Yeah this is how you do it using CLI.
