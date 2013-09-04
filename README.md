# cyphernet

secure replicatable tree database.

## a README Project

Currently, this is a readme project.
Really, cyphernet will be many projects that fit together.
Please check the issues to take part in discussions on aspects of this project.

## Synopsis

cyphernet is a abstraction of ideas present within various distributed systems
such as git, bittorrent, and bitcoin. There are three key aspects:

* objects are just binary blobs identified by their hash. This is known as a content addressable database.

* hashes inside an (e.g. JSON) object are pointers or "links" to other objects.
  thus, the database is a tree of hash pointers, called "cypherlinks".

* arbitary subsets of any two databases may be exchanged via merkle trees.
  (merkle trees allow two remote nodes to rapidly compare two sets of objects,
  and may then replicate by merging their sets)

## interesting properties

### Database is a tree, not a graph.

It is not possible for a cycle to form within a graph, unless there is a hash collision
(highly unlikely). So the graph must be a tree, and it is not necessary to check for cycles.

### Hashes always point backwards in time

You cannot create a hash, and then the object that produces the hash.
Instead, you must have the object, and then hash it.
So theirfore, cypherlinks always point backwards in time, to things that
already existsed before the document that they are contained within
was created.

### Immutability, Security, Distributability.

Since every object is referenced by it's hash, it's impossible to change (immutable).
If any one changed a piece of data, then it would change the hash.
If you have the hash of a document, then you can always verify it.
So, if you ask for a particular document ("follow a cypherlink"),
then it doesn't matter who you receive that document from!

This is the reverse of the security model of the www.
In most network applications, security is implemented by authorizing the connection.
But in a cyphernet, you use cryptographic hashes to authorise the _documents_,
but it is not necessary to authorize the connection.

This is rather like "authenticating" your friends by the sound of their voice,
not just becasue the call has come from the correct phone number. You can still verify
your friends identity if they call from a different phone, or even if you see them in person.

### Replication Partners

The cyphernet is still potentially secure when completely distributed.
There is no need to replicate via a central, or known server.
However, how to track peers, and initiate connections is out of scope of this project.
cyphernet is focusing on datastructures, security, and replication.
Network topology is another problem. There are other projects that
deal with creating connections between remote peers.
[peerjs](http://peerjs.com) and [ZeroTierOne](https://www.zerotier.com/)


### Defining a subset to replicate

The set of documents that define an "application" or "service" could be defined
in any number of ways. In git, a repository contains the data for just one
project, but you can replicate (checkout) just the branches you require.
With a blog, you'd want to replicate the text and images on each post, plus
the comments. On a social network, you'd want to replicate your mutual friends.

When two nodes connect, they will exchange a handshake that describes the set
of objects they wish to exchange. Then each pair traverses their database for
objects in that set. Then the difference between the two sets is found via
merkle tree. Finially, only the objects each node is missing is sent over
the wire!

_How_ a set is defined is up to the application, but most will involve
traversing the tree, or querying the indexed properties.

### Users & Authors

Using asymmetric key cryptography it is possible to verify the authorship of
documents. A user uploads an object, and then creates another signature
object, which links to the first object, the user's public key, and a signature
(of the object, with the key)

Signatures, and Keys, be replicated and stored within the database like any
other object.

## License

MIT
