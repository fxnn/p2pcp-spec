# p2pcp-spec

This repository describes an idea for a decentralized peer-to-peer computing platform (short p2pcp), especially providing a peer-to-peer social network.


## Idea

In terms of social networks, Client-Server is the web's most dominant architectural pattern.
While they were and are approaches to introduce new patterns of networking, the most successful websites -- Facebook, Twitter and the like -- follow the client-server pattern.
From the users perspective, this has advantages and disadvantages.
Amongst the latter ones is giving control and responsibility over all data and personal information to commercial companies, while being in need of trusting them to handle the data correctly and reliably providing the service now and in future.

A peer-to-peer architecture, where the data is distributed amongst all peers in the network, would turn this inside out.
Now, no one (or everybody) has control over the data, they are just distributed throughout the network.
This also causes problems, as it also opens the gates for criminals, providing them a platform to exchange all the data they want without having to fear control through centrailzed instances.

I do believe that one can find measures against criminals even in peer-to-peer networks.
I do also believe that even when no one will develop a software providing the advantages of peer-to-peer networking to the public, criminals will have means of secretly sharing data.

This spec aims at describing a decentralized computing platform, providing apps and data without a centralized infrastructure storing the data.
In this spec, all the important user data is stored amongst the peers.


## Components

At first we have a **user agent**.
This spec assumes that the user agent only has to satisfy the following requirements.

* The user accesses the p2pcp through a browser.
* The browser provides JavaScript, AJAX and storage APIs.
* Eventually, the user will have an asymmetric key pair loaded into his user agent, possibly through smartcard or crypto token.
* The user knows the entry address to one or multiple index servers.

The **index server** mediates between the user agents, only storing volatile information.
This means that one can always shutdown an index server, as the user agents will be able to login to another index server, effectively recreating the network.
Actually, a user agent should be able to connect to mulitple index servers at once, or at least, the index servers should be able to use clustering techniques (e.g. load balancing, sharding etc.).

The **user data** is distributed amongst all user agents as follows.

* Each block of data can uniquely be identified through a storage key.
* Each block of data has multiple URLs, pointing to all machines it's stored at.
  Everyone in possession of at least one of the URLs is able to _read_ that block of data.
  Everyone additionally in possession of a secret key is able to _write_ that block of data.
* Each index server keeps a mapping from storage keys to all known URLs.
* Each user agent keeps a list of all stored blocks of data, together with their storage keys.
* On login, the user agent sends the list of all his stored blocks of data to his index servers.
  From that time on, the index server can give the URL to that data block to anyone in possession of the storage key.
* As most user agents can't accept inbound connections, the index server will tunnel connections using websockets.
* The storage keys are usually derived from the asymmetric key pair.
  If a user wants to store information available for anyone knowing his public key, he encrypts a URI using his _public key_ and uses the result as storage key.
  For example, to provide profile information, he could encrypt the string "/profile" using his public key.
  Anyone knowing his public key can do that and thus access his profile information.
  The other way round, if a user wants to store private information, he encrypts a URI using his _private key_ and uses the result as storage key.
  For example, to store his notes, he could encrypt the string "/notes" using his private key.
  No one can do that, except for himself.
* Each block of data is stored in a redundant fashion.
  Therefore, a user can clear his browser data or login at another machine and still access his data.
  As this means to occupy other user agent's storage, there must be a system of balancing.
  Therefore, if a user wants to store _N_ data blocks with _R_ copies each, he must provide _N * R_ data blocks of his own storage to the network.
  _N_ of these blocks will be used for his own data, and _N * (R-1)_ blocks will be used for data of other users.
* A user can provide storage on foreign servers, as long as it's his own storage -- for example his Amazon S3 storage.
* Before occupying a data block, a user must provide his blocks to the network.
  Each index server keeps a list of free blocks' URLs.
  On login, the user agent provides his free blocks to the index server.
  On occupying space, the index server checks whether the user agent provides enough free space to other users.
