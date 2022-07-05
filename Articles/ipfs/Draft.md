# Draft

**Part 1**

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

The way IPFS solves this issue is by using a decentralised network of nodes to share data.

Every node in the IPFS network acts as a **host and a consumer** of data.

**Part 2:** Under the hood for IPFS

Let’s look step by step into what we need to achieve this:

### Content Addressing

The first puzzle piece in solving the problem is to use a globally agreed upon name which is unique for each chunk of data. This unique identifier is known as **CID** or **Content  Identifier.** 

CIDs are based on the content’s **[cryptographic hash](https://docs.ipfs.io/concepts/hashing/)**. That means:

- Any difference in the content will produce a different CID and
- The same content added to two different IPFS nodes using the same settings will produce *the same CID*.
- The CID can act as the fingerprint to verify integrity of the data once you receive it.

Now that we’ve solved the naming problem, let’s move on to how to arrange data across nodes.

### Storing the Data - Blocks

If we want to store a large chunk of data, say 2 TB in size, it is impractical for it be stored in one piece. Thus IPFS often splits the content into *blocks.* This means that different parts of a file can come from various locations making retrieval fast.
For those who’re interested, the data structure used for splitting and storing data is a DAG or Directed Acyclic Graph.We won’t be going into details here, but you can think of a file being split into chunks and stored in a tree sort of structure (not quite a tree, but a DAG).

![image](https://user-images.githubusercontent.com/75270610/177383465-5dcb0e0c-8cb9-4151-b747-cf697e5fa3a7.png)

(This illustration was built using the [dag visualiser](https://dag.ipfs.io/) by ipfs.)

Now that we’ve split data into easy-to-retrieve blocks, we move onto how to find where each block is.

### Finding Blocks - DHT

In a typical server the name of a file and where it is stored might be saved in a hash table of the sort as follows.

| Name | Location |
| --- | --- |
| Cat.jpg | Alice’s server |
| Dog.png | Bob’s server |

Now, such a table is generally centralised and most commonly the entry point for most applications. However, remember that in decentralised networks, we can have *no central authority* keeping track of which node keeps which data.

The answer? Just make your hash table distributed aka, a **Distributed Hash Table** or **DHT** for short.

Do keep in mind that every node does not store every key-value pair in the DHT.
When a node needs to access a data, it will essentially ask around *who has this content* until it finds the peer which is hosting that data. This Lookup is optimised by asking peers whose PeerIDs are similar to the CIDs that you’re looking for.

Once we’ve found which Peer Node has the content we’re looking for, it’s time to get the data!

### Getting Blocks - Bitswap

To send and receive blocks across the ipfs network, bitswap is used.

Bitswap has two main jobs:

- Acquire blocks requested by the client from the network.
- Send blocks in its possession to other peers who want them.

When nodes running the Bitswap protocol want to fetch a file, they send out `want-lists`
 to other peers. Those nodes which have the data, respond with a `have` and then the node sends a `want-block` to get the block.

Remember how we were talking about how a file is split into a tree sort of structure (not a tree, but a DAG). The blocks we get back using bitswap themselves might be links to next set of blocks in the next level of the graph, thus the node will recursively get blocks until it gets the entire file.
An alternative to this, which is currently in development is called Graphsync, which will automatically fetch all child blocks for a given root block.

Once we get the block, we can verify its integrity by hashing the content and matching its CID against the one we want.

To summarise, IPFS

1. First splits files into blocks.
2. Each block gets a unique CID.
3. To get data, we first lookup which peer is hosting the block we’re interested in.
4. Then we use bitswap to get the block.

**And that’s it! we’ve successfully stored and retrieved data in a decentralised network. Woohoo!**

**//Part 3: Terminology**

Now let’s look at something interesting called *pinning*.

### Pinning

We mentioned in the beginning that every node in the IPFS network acts as a host and a consumer for data. The way this works is, whenever a node requests blocks from the network and receives them, it now caches this block in its storage and starts serving these new blocks, that is, other peers can now request data from this node.

As new data continues to be accessed, old blocks might get deleted by the Garbage Collector. But what if we don’t want that to happen? What if we want to specifically store some data we care about in our local node? This is where pinning comes in.

**Pinning** essentially means just one thing, Don’t delete these contents from my local node. Instead keep them saved. This means that our local node will forever keep the files we’ve pinned and is guaranteed to serve them.

If you run an IPFS node and intend to host say, your website on it, Typically you would pin you website files to your node.

### Deleting

Once you’ve sent some data into the network, there is no actual way to delete it. This is because each node decides what they want to keep and what they want to host. This is how things are currently with the web as well, if you sent a file to someone, and later on say, deleted the website or removed access for it from them, it is still entirely possible that they might have saved the file locally, uploaded it and/or sent them to someone else.

It is possible for you to delete unwanted blocks from your local node though.

**Part 3:** How to use IPFS?

- For storage
    - Similar to Hard Drive
- For proof
    - Once you store something on ipfs and get a hash you are sure that the filie is not tampered with if its hash is the same

**Part 4:** What makes IPFS uncensorable?

(re-look into opt-in censor list)
