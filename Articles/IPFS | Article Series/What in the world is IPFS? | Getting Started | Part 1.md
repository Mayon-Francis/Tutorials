Web 3 is really big right now. You’ve probably heard of writing programs (aka smart contracts) in chains like Ethereum and other prominent networks. But what about storing big chunks of data? Something like a 4K video, or a cat image for example. How do we go on about storing those in decentralised networks?

The go-to way of storing data as such is by using something known as IPFS.

## So, W**hat is IPFS?**

Interplanetary File System or IPFS for short is a protocol (and a peer to peer network) for storing and accessing files in a decentralised web instead of using a centralised server.

So before we deep dive into what IPFS is, let’s take a look at how things work in the current Web2.0.
Imagine you want to access a file that is stored in a sever. This server might be serving requests that come to the domain, say example.com. 
If a file `Cat.jpg` exists in that server and you want to access it, you might go to an address such as `https://example.com/Cat.jpg.` This is known as Location based addressing as you just requested a particular address (example.com) for a particular image. 

The issue with such an approach is

- You’re asking one centralised server to send a response data
- It is entirely possible that the server returns a dog image even though you asked for a cat image.

The way IPFS solves this issue is by using:

1. **A decentralised network of nodes to share data.**
What this means is that when you upload some data to the ipfs network, it is not being sent to a central server, but instead is stored across the decentralised network .
2. **Naming data with respect to their content**
Every piece of data in the ipfs network is given its own unique identifier know as **CID** or **Content Identifier.** This means a file name will be indicative of the data it contains and any change in the data will make a change in the CID as well, thereby acting as a means to verify the integrity of the file.

To get started with using ipfs, you can visit the [wikipedia mirror](https://ipfs.io/ipfs/QmXoypizjW3WknFiJnKLwHCnL72vedxjQkDDP1mXWo6uco/wiki/Wikipedia.html) hosted on the ipfs network.

The link above is known as a gateway. There are many public gateways available, with the one above (`https://ipfs.io/`) being from the official ipfs team.

### Installing IPFS Desktop

To interact directly with the network and to store data in it, we can use the IPFS Desktop application.  [Install it from here.](https://docs.ipfs.io/install/ipfs-desktop/)

You’ll be greeted with an interface like this:

![image](https://user-images.githubusercontent.com/75270610/177675246-bcc33ef1-0bc2-4cbb-b9bc-083fd645f3fa.png)

Go to the Files Tab and click the **Import** button to upload a file.

Once you’ve uploaded it, use the more icon in the side, to get it’s public shareable link using the ipfs gateway (similar to this: [https://ipfs.io/ipfs/QmYKEp564QwWJEUuzAy4s8VcPnptFNGygaM7FoMaZkoC5t?filename=Cat.jpeg](https://ipfs.io/ipfs/QmYKEp564QwWJEUuzAy4s8VcPnptFNGygaM7FoMaZkoC5t?filename=Cat.jpeg))

You can also copy its CID and share it, which can be used by other peers to get the image.

### Installing IPFS Companion

The ipfs suite also comes with a browser extension which will intercept `ipfs://` type links and automatically route them through your local node.

Install it using the following links

[Install it for Chrome](https://chrome.google.com/webstore/detail/ipfs-companion/nibjojkomfdiaoajekhjakgkdhaomnch)

[Install it for Firefox](https://addons.mozilla.org/firefox/addon/ipfs-companion/)

or go to the official website for more [instructions](https://docs.ipfs.io/install/ipfs-companion/#install).

When we’re using a public gateway, we’re receiving data from the public servers that the gateway exposes, which looks something like this:

![image](https://user-images.githubusercontent.com/75270610/178035618-73f28a45-ca2d-4190-8830-b71ff3b19872.png)

once you’ve installed ipfs companion, try opening the [above cat image link](https://ipfs.io/ipfs/QmYKEp564QwWJEUuzAy4s8VcPnptFNGygaM7FoMaZkoC5t?filename=Cat.jpeg) once again.

You’ll notice that your URL changes to something similar to this (will be a bit different):

`http://bafybeieuhbcjdmdlwzko4slkuhgd4ddtjauu64taecrxup5y3r3hpeymy4.ipfs.localhost:8080/?filename=Cat.jpeg`

This is ipfs companion altering the URL to use your local node. This means now you’re receiving the image directly from the network, and no third party gateways are being used!!

![image](https://user-images.githubusercontent.com/75270610/178035656-c5a02795-e93e-405a-9538-14b9c16c33c2.png)

And that’s it we’ve successfully used IPFS to store and view data.

In the Next part, we’ll be looking under the hood of ipfs to understand how it works.

<LINK TO PART 2>

If you’re interested in directly using ipfs in your apps, we’re going into more use case scenarios in part 3!

<LINK TO PART 3>
