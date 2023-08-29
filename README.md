# MyDegenToken
The purpose of this token contract programme is to develop an ERC20 token for Degen Gaming and deploy it on the Avalanche network along with the functionality listed in the project requirements.
## Description

This programme is a straightforward token contract for producing an ERC20 token that will be used for degenerate gambling on the avalanche network. The following functions are included for the degen token: creating new tokens, with the restriction that only the owner may mint new tokens, and distributing them to players as rewards; transferring tokens to enable participants to trade their tokens with other people; redeemed tokens, allowing users to exchange their tokens for goods in the in-game shop; Having access to their token balance at all times, players should be able to check their token balance; Burning tokens makes it possible for anyone to get rid of any extra ones they may have.
Additionally, the matching account address will be pasted into the snowtrace testnet to track the functionality of the various functions. This project uses an injected provider as its environment.

## Getting Started
### Executing program
       
```javascript
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

contract MyDegenToken is IERC20 {
    string public name = "MyCustomToken";
    string public symbol = "MCT";
    uint8 public decimals = 0;
    uint256 private _totalSupply = 0;
    mapping(address => uint256) private _balances;
    mapping(address => mapping(address => uint256)) private _allowances;
    address private _owner;

    // Store
    mapping(uint256 => uint256) private _itemPrices;
    uint256 private _itemCount = 0;

    event Mint(address indexed account, uint256 amount);
    event Burn(address indexed account, uint256 amount);
    event Redeem(address indexed account, uint256 amount);
    event AddItem(uint256 itemId, uint256 price);
    event BuyItem(address indexed buyer, uint256 itemId);

    modifier onlyOwner() {
        require(msg.sender == _owner, "Only the owner can perform this action");
        _;
    }

    constructor() {
        _balances[msg.sender] = _totalSupply;
        _owner = msg.sender;
        emit Transfer(address(0), msg.sender, _totalSupply);
    }

    function totalSupply() external view override returns (uint256) {
        return _totalSupply;
    }

    function balanceOf(address account) external view override returns (uint256) {
        return _balances[account];
    }

    function transfer(address recipient, uint256 amount) external override returns (bool) {
        _transfer(msg.sender, recipient, amount);
        return true;
    }

    function allowance(address owner, address spender) external view override returns (uint256) {
        return _allowances[owner][spender];
    }

    function approve(address spender, uint256 amount) external override returns (bool) {
        _approve(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(address sender, address recipient, uint256 amount) external override returns (bool) {
        _transfer(sender, recipient, amount);
        uint256 currentAllowance = _allowances[sender][msg.sender];
        require(currentAllowance >= amount, "ERC20: transfer amount exceeds allowance");
        _approve(sender, msg.sender, currentAllowance - amount);
        return true;
    }

    function mint(address account, uint256 amount) external onlyOwner returns (bool) {
        require(account != address(0), "ERC20: mint to the zero address");
        _totalSupply += amount;
        _balances[account] += amount;
        emit Mint(account, amount);
        emit Transfer(address(0), account, amount);
        return true;
    }

    function burn(uint256 amount) external returns (bool) {
        require(amount <= _balances[msg.sender], "ERC20: burn amount exceeds balance");
        _balances[msg.sender] -= amount;
        _totalSupply -= amount;
        emit Burn(msg.sender, amount);
        emit Transfer(msg.sender, address(0), amount);
        return true;
    }

    function redeem() external returns (bool) {
        uint256 amount = _balances[msg.sender];
        require(amount > 0, "ERC20: redeem amount is zero");
        _balances[msg.sender] = 0;
        _totalSupply -= amount;
        emit Redeem(msg.sender, amount);
        emit Transfer(msg.sender, address(0), amount);
        return true;
    }

    function _transfer(address sender, address recipient, uint256 amount) internal {
        require(sender != address(0), "ERC20: transfer from the zero address");
        require(recipient != address(0), "ERC20: transfer to the zero address");
        require(amount <= _balances[sender], "ERC20: transfer amount exceeds balance");

        _balances[sender] -= amount;
        _balances[recipient] += amount;
        emit Transfer(sender, recipient, amount);
    }

    function _approve(address owner, address spender, uint256 amount) internal {
        require(owner != address(0), "ERC20: approve from the zero address");
        require(spender != address(0), "ERC20: approve to the zero address");

        _allowances[owner][spender] = amount;
        emit Approval(owner, spender, amount);
    }

    // Add item to the store
    function addItem(uint256 price) external onlyOwner {
        _itemCount++;
        _itemPrices[_itemCount] = price;
        emit AddItem(_itemCount, price);
    }

    // Buy item from the store
    function buyItem(uint256 itemId) external {
    require(itemId > 0 && itemId <= _itemCount, "Invalid item ID");
    require(_itemPrices[itemId] > 0, "Item not available");

    uint256 itemPrice = _itemPrices[itemId];
    require(_balances[msg.sender] >= itemPrice, "Insufficient balance");

    _balances[msg.sender] -= itemPrice;
    _totalSupply -= itemPrice;  // Burn the purchased amount

    emit Transfer(msg.sender, address(0), itemPrice);
    emit BuyItem(msg.sender, itemId);
}


    // Check the price of an item
    function getItemPrice(uint256 itemId) external view returns (uint256) {
        require(itemId > 0 && itemId <= _itemCount, "Invalid item ID");
        return _itemPrices[itemId];
    }

    // Get the owner's address
    function getOwner() external view returns (address) {
        return _owner;
    }
}                         
```
To compile the code, click on the "Solidity Compiler" tab in the left-hand sidebar. Make sure the "Compiler" option is set to "0.8.4" (or another compatible version), and then click on the "Compile V_eth_4.sol" button.

Once the code is compiled, you can deploy the contract by clicking on the "Deploy & Run Transactions" tab in the left-hand sidebar. Select the "MyDegenToken" contract from the dropdown menu, and then click on the "Deploy" button. 

Once the contract is deployed, you can interact with it by clicking on various functions. Click on the "MyDegenToken" contract in the left-hand sidebar, and then click on the functions for transactions, getting the results accordingly.

## Authors
Vidhika Bajaj

## License
This project is licensed under the MIT License
