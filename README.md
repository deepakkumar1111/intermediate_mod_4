# ETH Intermediate Module-4

This Solidity contract shows the implementation of a smart contract using the token named "Degen" with abbreviation "DGN" over the testnet of AVALANCHE named as Fuji.

## Description

This project demonstrates an intermediate-level Solidity contract that includes functionalities such as minting, transferring, and burning tokens. It also includes a system for adding and redeeming items, showcasing how to implement more complex smart contract features. This contract serves as a practical example for developers aiming to build more advanced decentralized applications on the Ethereum blockchain.


## Getting Started

### Prerequisites

- Solidity ^0.8.0
- Remix IDE or any other Solidity development environment
- MetaMask or any other Ethereum-compatible wallet

### Executing Program

To run this program, you can use Remix, an online Solidity IDE. To get started, go to the Remix website at [Remix IDE](https://remix.ethereum.org/).

1. **Create a New File:**
   - Click on the "+" icon in the left-hand sidebar.
   - Save the file with a .sol extension (e.g., DefiEmpire.sol).
   - Copy and paste the following code into the file:

```solidity
pragma solidity 0.8.19;

contract ERC20 {
    address public immutable owner;
    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    struct Item {
        uint itemId;
        string itemName;
        uint itemPrice;
    }
    
    mapping(uint => Item) public items;
    uint public itemCount;

    mapping(address => mapping(uint => bool)) public redeemedItems;

    event ItemRedeemed(address indexed user, uint indexed itemId, string itemName, uint itemPrice);
    event Transfer(address indexed from, address indexed to, uint amount);

    constructor() {
        owner = msg.sender;
        totalSupply = 0;
        _addItem("Sword", 100);
        _addItem("Shield", 150);
        _addItem("Potion", 50);
    }

    modifier onlyOwner {
        require(msg.sender == owner, "Only the contract owner can execute this function");
        _;
    }

    string public constant name = "Degen";
    string public constant symbol = "DGN";
    uint8 public constant decimals = 10;

    function transfer(address recipient, uint amount) external returns (bool) {
        require(balanceOf[msg.sender] >= amount, "The balance is insufficient");
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function mint(address receiver, uint amount) external onlyOwner {
        balanceOf[receiver] += amount;
        totalSupply += amount;
        emit Transfer(address(0), receiver, amount);
    }

    function burn(uint amount) external {
        require(amount > 0, "Amount should not be zero");
        require(balanceOf[msg.sender] >= amount, "The balance is insufficient");
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
    
    function addItem(string memory itemName, uint256 itemPrice) external onlyOwner {
        _addItem(itemName, itemPrice);
    }

    function _addItem(string memory itemName, uint256 itemPrice) internal {
        itemCount++;
        Item memory newItem = Item(itemCount, itemName, itemPrice);
        items[itemCount] = newItem;
    }

    function getItems() external view returns (Item[] memory) {
        Item[] memory allItems = new Item[](itemCount);
        
        for (uint i = 1; i <= itemCount; i++) {
            allItems[i - 1] = items[i];
        }
        
        return allItems;
    }
    
    function redeem(uint itemId) external {
        require(itemId > 0 && itemId <= itemCount, "Invalid item ID");
        Item memory redeemedItem = items[itemId];
        
        require(balanceOf[msg.sender] >= redeemedItem.itemPrice, "Insufficient balance to redeem");
        require(!redeemedItems[msg.sender][itemId], "Item already redeemed");

        balanceOf[msg.sender] -= redeemedItem.itemPrice;
        balanceOf[owner] += redeemedItem.itemPrice;

        redeemedItems[msg.sender][itemId] = true;

        emit Transfer(msg.sender, owner, redeemedItem.itemPrice);
        emit ItemRedeemed(msg.sender, itemId, redeemedItem.itemName, redeemedItem.itemPrice);
    }
}
```

2. **Compile the Code:**
   - Click on the "Solidity Compiler" tab in the left-hand sidebar.
   - Make sure the "Compiler" option is set to "0.8.19" (or another compatible version).
   - Click on the "Compile DefiEmpire.sol" button.

3. **Deploy the Contract:**
   - Click on the "Deploy & Run Transactions" tab in the left-hand sidebar.
   - Select the "ERC20" contract from the dropdown menu.
   - Click on the "Deploy" button.

4. **Interact with the Contract:**
   - Once the contract is deployed, you can interact with it by calling various functions such as `mint`, `burn`, `transfer`, `getItems`, and `redeem`.
   - For example, to mint new tokens, click on the `mint` function, enter the receiver's address and the amount, and then click on the "transact" button.

## Authors

Deepak kumar

## License

This project is licensed under the MIT License - see the LICENSE.md file for details.
