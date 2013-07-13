# Weird Idea

so… I've had this weird idea recently...

In git (and in other secure + distributed systems) you have
a tree of hashes where each object is identified by it's hash
and objects contain pointers to other objects.
They just have the hash of other objects stored inside them.

(this is known as a Content Addressable Store,
but there does not seem to be any terminology
for the design pattern of hash pointers)

Have been calling these hash pointers
CYPHERLINKS (because it sounds much cooler)

git is based on cypherlinks,
and so is bittorrent
and bitcoin! - couchdb uses cypherlinks to version documents.

The way that couchdb uses cypher links is much simpler than git.

Git has 3 types of objects - commits, trees, and blobs.
blobs are just snapshots of files,
trees are snapshots of a directory and link to blobs and subtrees,
commits link to a tree and a previous commit (unless it's the first version)

In couchdb - each document links to the previous version of that document.

the cypherlink has some interesting properties:

* cypherlinks are immutable - if you "update" a document, it's now a different document.
  instead, associate it with the previous version - cypherlink to the previous version.

* cypherlinks always point back in time. You have to know the value of an object before you can link it.

* Thus, it's impossible to create cycles - because this is impossible: `A === Hash(A + B)` for any non empty `A, B`

Previously, people (including myself) have been working on "git for data",
replicating the architecture of git (commits, merges, etc)

But now, I'm thinking that the best idea is just to build a system for working with cypherlinks,
and then using that to build git, if you want, or some arbitrary system.

The other thing that you get with cypherlinks, is easy replication.
You just have to figure out what you have that I don't have, and then send me that.
You can use a [merkle tree](http://en.wikipedia.org/wiki/Merkle_tree) for that,
I wrote a [module for that](https://github.com/dominictarr/level-merkle) last weekend.

## the new idea

All data is stored in the same "bucket", immutable, and addressed my it's hash.

### Query by Searching

Store objects in JSON, by their hashes, indexing everything.
Sure it might take 3 times the space to store your data,
but you don't have to write queries.

### Traverse the cypherlinks

Build well defined sets of objects by traversing cypherlinks.
If `X` cypherlinks to `Y`, `Y` is a part of `X`.
You can index and traverse backlinks too,
but you can't validate those documents,
or verify that you have all of them.

If you want to certify a collection of backlinked objects,
create a new document that cypherlinks to them.

### replicate with merkle trees

With a merkle tree, it's easy to replicate any (sub) set of objects.
Hashing is pretty fast,
(0.4 seconds to hash 30mb on my laptop - that could be 1.5 million records!)
so it's would be pretty easy to build a hash tree just per replication.

You could select a subset by search, or by traversing a cyphertree,
then you could replicate that like git.

Except it would be pretty straightforward to build say, a blog,
where there is a markdown object, some JSON metadata that pointed to it,
then you sign it with crypto, and later people check this out, and add comments
that link back to the article.

They could send these comments back to your article, and by adding a new tree,
you'd collect everything into that.

## getting really crazy...

But it doesn't need to stop there.
hashed objects do not collide, so you could just put all the objects in the same namespace!
I mean, all the objects on the internet!

There needs be no bounded database,
just trees of cryptographically signed data floating in the ether...