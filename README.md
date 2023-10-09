# ERC721A

## Saving Gas with ERC721A Batch Minting

### Optimizations  

1. Removing duplicate storage from OpenZeppelin’s (OZ) ERC721Enumerable.  
2. Updating the owner’s balance once per batch mint request, instead of per minted NFT.  
3. Updating the owner data once per batch mint request, instead of per minted NFT.  

### Implementation Details  

- In the ERC721A _safeMint implementation, owner balances are updated only once, no matter how many NFTs are minted in the batch call.  
- In the base ERC721 contract before, this assignment would have had to be done in a loop, one time for each NFT being minted in the batch group. Now it’s done in a single update.  

### Additional Costs  

The tradeoff of the ERC721A contract design is that `transferFrom` and `safeTransferFrom` transactions cost more gas, which means it may cost more to gift or sell an ERC721A NFT after minting.  

The ERC721A `_safeMint` logic makes efficiency gains by not setting explicit owners of specific tokenIDs when they are consecutive IDs minted by the same owner.  

However, this means that transferring a tokenID that does not have an explicit owner address set, the contract has to run a loop across all of the tokenIDs until it reaches the first NFT with an explicit owner address to find the owner that has the right to transfer it, and then set a new owner, thus modifying ownership state more than once to maintain correct groupings.  

### Further Reading  

- [Introducing ERC721A](https://www.azuki.com/erc721a)  
- [ERC721 vs ERC721A](https://www.alchemy.com/blog/erc721-vs-erc721a-batch-minting-nfts)  

## Why ERC721 enumerable’s implementation shouldn’t be used on-chain  

- Keeping the additional mappings needed to enumerate through tokens owned by a single address is very gas intensive. Transfering tokens requires additional modifications to these mappings to update enumeration list.  

Solutions:  
- If you do not absolutely need on-chain Enumerable, do not implement Enumerable.  
- Use The Graph or any other tool (Alchemy has centralized APIs) to index all items of an user.  

### Further Reading  

- [ERC721Enumerable OZ Implementation YouTube](https://www.youtube.com/watch?v=hL5uPgEAuIo) 
- [ERC721Enumerable OpenZeppelin Docs](https://docs.openzeppelin.com/contracts/4.x/api/token/erc721#IERC721Enumerable)  
- [ERC721Enumerable Code](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC721/extensions/ERC721Enumerable.sol)  
- [ERC721Enumerable Twitter Thread](https://twitter.com/dievardump/status/1492595244055027712)  