visit https://web3js.readthedocs.io/en/v1.3.1/web3-eth-personal.html for more details

There are multiple levels of message you need to be aware of.
Actual Message: The thing you care about, such a string like “This is Alice’s message” or the number 17 encoded as one byte, 0x11.
Actual Message’s Hash: Partly because Solidity has poor string manipulation, OpenZeppelin strongly suggests that you hash your Actual Message, and, operate solely on the Message Hash. You do this in solidity with the simple.
more details in https://web3js.readthedocs.io/en/v1.3.1/web3-eth-personal.html
keccak256(abi.encodePacked(…)).
Wrapped Message: web3’s signing tools (web3.eth.sign and web3.accounts.sign) automatically implement a safety feature: before a message is signed, the string “\x19EthereumSignedMessage\n…” + length of the message is prepended to form a Wrapped Message. This is to prevent a user from accidentally signing what she thinks is a plain old message, but is in fact an encoded transaction to be run on Ethereum. web3 allows you to wrap any arbitrary string, but, that is a bad idea because it does not match what OpenZeppelin does.
There’s also a subtler issue here — Solidity has incomplete string manipulation tools, so, it’s actually not possible to generate a string containing the base10 numerals that represent the length of the abitrary string.

Also, web3 has the “helpful” polymorphism that if you pass web3.eth.sign anything other than a hex encoded string, it will interpret it as a string, but if you pass a hex encoded string, it will interpret it as a byte array. Note that padding matters (I think). 0x0001 and 0x01 are the same number, but they are not the same byte array [0x00, 0x01] versus [0x01]. Net net: Ignore the option of sending arbitrary string. Stick with passing a 66 character string representing a 32 byte array, e.g.:. ‘0x1111111111111111111111111111111111111111111111111111111111111111’
Wrapped Message’s Hash. The Wrapped Message is hashed into 32 bytes. This is the second time hashes are used. This can make it pretty confusing when looking at the OpenZeppelin.ECDSA library. The param is named hash in the two function, but, represent very different things.

let me stop here.
more details in https://web3js.readthedocs.io/en/v1.3.1/web3-eth-personal.html