## DNS Lookup

The first step of navigating to a web page is finding where the assets for that page are located.

Your browser requests a DNS lookup, which is eventually fielded by a name server, which in turn responds with an IP address. After this initial request, the IP will likely be cached for a time, which speeds up subsequent requests by retrieving the IP address from the cache instead of contacting a name server again.

## TCP Handshake

Once the IP address is known, the browser sets up a connection to the server via a [TCP three-way handshake](https://developer.mozilla.org/en-US/docs/Glossary/TCP_handshake). TCP's three-way handshaking technique is often referred to as "SYN-SYN-ACK" — or more accurately SYN, SYN-ACK, ACK — because there are three messages transmitted by TCP to negotiate and start a TCP session between two computers.

## Response

Once we have an established connection to a web server, the browser sends an initial [HTTP `GET` request](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) on behalf of the user, which for websites is most often an HTML file.

## Congestion control / TCP Slow Start

TCP packets are split into segments during transmission. Because TCP guarantees the sequence of packets, the server must receive an acknowledgment from the client in the form of an ACK packet after sending a certain number of segments.

If the server waits for an ACK after each segment, that will result in frequent ACKs from the client and may increase transmission time, even in the case of a low-load network.

On the other hand, sending too many segments at once can lead to the problem that in a busy network the client will not be able to receive the segments and will just keep responding with ACKs for a long time, and the server will have to keep re-sending the segments.

In order to balance the number of transmitted segments, the [TCP slow start](https://developer.mozilla.org/en-US/docs/Glossary/TCP_slow_start) algorithm is used to gradually increase the amount of transmitted data until the maximum network bandwidth can be determined, and to reduce the amount of transmitted data in case of high network load.

The number of segments to be transmitted is controlled by the value of the congestion window (CWND). 
If an ACK is received, then the CWND value will be doubled, and so the server will be able to send more segments the next time. If instead no ACK is received, then the CWND value will be halved.

## Parsing

Once the browser receives the first chunk of data, it can begin parsing the information received. [Parsing](https://developer.mozilla.org/en-US/docs/Glossary/Parse) is the step the browser takes to turn the data it receives over the network into the [DOM](https://developer.mozilla.org/en-US/docs/Glossary/DOM) and [CSSOM](https://developer.mozilla.org/en-US/docs/Glossary/CSSOM), which is used by the renderer to paint a page to the screen.


## Building the DOM tree

The first step is processing the HTML markup and building the DOM tree. HTML parsing involves [tokenization](https://developer.mozilla.org/en-US/docs/Web/API/DOMTokenList) and tree construction.

When the parser finds non-blocking resources, such as an image, the browser will request those resources and continue parsing. Parsing can continue when a CSS file is encountered, but `<script>` elements — particularly those without an [`async`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) or `defer` attribute — block rendering, and pause the parsing of HTML..