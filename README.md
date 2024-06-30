# ETH Intermideate Module-4
### This Contract shows the implementation of the smart contract using the token named "Degen" with abbriviation as "DGN" overn the testnet of AVALANCHE named as fuji.

Contract Initialization

```
// SPDX-License-Identifier: MIT
pragma solidity 0.8.19;

contract ERC20 {
```

address mapping and structure initialization
```
    address public immutable owner;
    uint public totalSupply;
    mapping (address => uint) public balanceOf;

    struct Item {
        uint itemId;
        string itemName;
        uint itemPrice;
    }
    
    mapping(uint => Item) public items;
    uint public itemCount;

    // Mapping to track redeemed items for each user
    mapping(address => mapping(uint => bool)) public redeemedItems;
```
Event declaration
```
    // Event to log item redemption
    event ItemRedeemed(address indexed user, uint indexed itemId, string itemName, uint itemPrice);

    // Event to log token transfers
    event Transfer(address indexed from, address indexed to, uint amount);
```
Constructor creation```
    constructor() {
        owner = msg.sender;
        totalSupply = 0;
    }
```
Owner privilage modifier
``` 
    modifier onlyOwner {
        require(msg.sender == owner, "Only the contract owner can execute this function");
        _;
    }
```
Token creation
```
    string public constant name = "Degen";
    string public constant symbol = "DGN";
    uint8 public constant decimals = 10;
```
Transfer function
```
    function transfer(address recipient, uint amount) external returns (bool) {
        require(balanceOf[msg.sender] >= amount, "The balance is insufficient");

        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;

        emit Transfer(msg.sender, recipient, amount);
        return true;
    }
```
Mint function
```
    function mint(address receiver, uint amount) external onlyOwner {
        balanceOf[receiver] += amount;
        totalSupply += amount;
        emit Transfer(address(0), receiver, amount);
    }
```	
Burn function
```
    function burn(uint amount) external {
        require(amount > 0, "Amount should not be zero");
        require(balanceOf[msg.sender] >= amount, "The balance is insufficient");
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;

        emit Transfer(msg.sender, address(0), amount);
    }
```
function to Add items to structure
```	
    function addItem(string memory itemName, uint256 itemPrice) external onlyOwner {
        itemCount++;
        Item memory newItem = Item(itemCount, itemName, itemPrice);
        items[itemCount] = newItem;
    }
```
Get item function to print all the current items in the structure\
```
    function getItems() external view returns (Item[] memory) {
        Item[] memory allItems = new Item[](itemCount);
        
        for (uint i = 1; i <= itemCount; i++) {
            allItems[i - 1] = items[i];
        }
        
        return allItems;
    }
 ```
function to redeem items from the structure
``` 
    function redeem(uint itemId) external {
        require(itemId > 0 && itemId <= itemCount, "Invalid item ID");
        Item memory redeemedItem = items[itemId];
        
        require(balanceOf[msg.sender] >= redeemedItem.itemPrice, "Insufficient balance to redeem");
        require(!redeemedItems[msg.sender][itemId], "Item already redeemed");

        balanceOf[msg.sender] -= redeemedItem.itemPrice;
        balanceOf[owner] += redeemedItem.itemPrice;

        // Mark the item as redeemed for the user
        redeemedItems[msg.sender][itemId] = true;

        emit Transfer(msg.sender, owner, redeemedItem.itemPrice);
        emit ItemRedeemed(msg.sender, itemId, redeemedItem.itemName, redeemedItem.itemPrice);
    }
}
```
This concludes the smart contract code for the ETH-Intermedeate-Module-4🍀
