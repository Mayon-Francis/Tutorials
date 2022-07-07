In the previous article (LINK TO PREVIOUS ARTICLE) we understood the basics of what ipfs is and how to use it, with the help of **IPFS Desktop** and **IPFS Companion**.

Now, we’ll be focusing on how ipfs works under the hood and understand some terminology related to it.

### 1. **Content Addressing**

We touched upon **CID** or **Content Identifier** in the previous article.

To expand on it, the unique identifier for each piece of data stored on IPFS is known as CID.

CIDs are based on the content’s **[cryptographic hash](https://docs.ipfs.io/concepts/hashing/)**. That means:

- Any difference in the content will produce a different CID and
- The same content added to two different IPFS nodes using the same settings will produce *the same CID*.
    
    Try downloading the [same cat image](https://ipfs.io/ipfs/QmYKEp564QwWJEUuzAy4s8VcPnptFNGygaM7FoMaZkoC5t?filename=Cat.jpeg) in the previous article and upload it from your local IPFS Desktop. You’ll see that it generates the same CID. <VERIFICATION NEEDED>
    
- The CID can act as the fingerprint to verify integrity of the data once you receive it.
    
    When you accessed the cat image through your local node, it first downloaded the file, then it verified the CID against the hash of the file and confirmed that the file that you received was indeed the one you requested for.
    
    This integrity verification is done through the local node, and hence if you use a public gateway, you’ll be trusting it provide the data.
    

### 2.  **BLOCKS - Storing Data**

If we want to store a large chunk of data, say 2 TB in size, it is impractical for it be stored in one piece. Thus IPFS often splits the content into *blocks.* This means that different parts of a file can come from various locations making retrieval fast.
For those who’re interested, the data structure used for splitting and storing data is a **DAG** or **Directed Acyclic Graph**.We won’t be going into details here, but you can think of a file being split into chunks and stored in a tree sort of structure (not quite a tree, but a DAG).

![image](https://user-images.githubusercontent.com/75270610/177675493-b3be95ca-59f7-4945-9280-2d8e8f67ad7c.png)

(This illustration was built using the [dag visualiser](https://dag.ipfs.io/) by ipfs.)

Now that we’ve split data into easy-to-retrieve blocks, we move onto how to find where each block is.

### Finding Blocks - DHT

In a typical server the name of a file and where it is stored might be saved in a hash table of the sort as follows.

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
