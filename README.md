# ERC20 ana Vault Token Smart Contracts
This Solidity project demonstrates the implementation of a basic ERC20 token and a Vault contract for managing deposits and withdrawals of the token. The purpose of this project is to showcase the functionality of ERC20 tokens and how they can be utilized within a Vault to handle token transactions securely.
## Description
The project consists of two main contracts: ERC20 and Vault.
ERC20 Contract: This contract implements a basic ERC20 token with standard functionalities such as transfer, approval, transferFrom, mint, and burn. The token is named "Solidity by Example" with the symbol "SOLBYEX".
Vault Contract: This contract allows users to deposit and withdraw the ERC20 token. It keeps track of user balances in terms of "shares" that represent their portion of the total deposits. The Vault contract interacts with the ERC20 token to manage deposits and withdrawals.
## Getting Started
### Executing program
To run this program, you can use Remix, an online Solidity IDE. To get started, go to the Remix website at https://remix.ethereum.org/.
Once you are on the Remix website, create a new file by clicking on the "+" icon in the left-hand sidebar. Save the file with a .sol extension (e.g., HelloWorld.sol). Copy and paste the following code into the file:
Deploying and Interacting with the Contracts
To deploy and interact with these contracts, you can use Remix, an online Solidity IDE. Follow the steps below:
Step 1: Deploy the ERC20 Contract
1.Go to Remix.
2.Create a new file named ERC20.sol.
3.Copy and paste the following code into ERC20.sol:
```javascript
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;
contract DragonToken {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "Dragon Token";
    string public symbol = "DTN";
    uint8 public decimals = 18;
    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }
    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }
    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }
    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }
    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```
4.Compile the ERC20.sol contract.
5.Deploy the ERC20 contract.
Step 2: Deploy the Vault Contract
1.Create a new file named Vault.sol.
2.Copy and paste the following code into Vault.sol:
```javascript
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;
interface IERC20 {
    function totalSupply() external view returns (uint);
    function balanceOf(address account) external view returns (uint);
    function transfer(address recipient, uint amount) external returns (bool);
    function allowance(
        address owner,
        address spender
    ) external view returns (uint);
    function approve(address spender, uint amount) external returns (bool);
    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool);
    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}
contract Vault {
    IERC20 public immutable token;
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    constructor(address _token) {
        token = IERC20(_token);
    }
    function _mint(address _to, uint _shares) private {
        totalSupply += _shares;
        balanceOf[_to] += _shares;
    }
    function _burn(address _from, uint _shares) private {
        totalSupply -= _shares;
        balanceOf[_from] -= _shares;
    }
    function deposit(uint _amount) external {
        /*
        a = amount
        B = balance of token before deposit
        T = total supply
        s = shares to mint
        (T + s) / T = (a + B) / B 
        s = aT / B
        */
        uint shares;
        if (totalSupply == 0) {
            shares = _amount;
        } else {
            shares = (_amount * totalSupply) / token.balanceOf(address(this));
        }
        _mint(msg.sender, shares);
        token.transferFrom(msg.sender, address(this), _amount);
    }
    function withdraw(uint _shares) external {
        /*
        a = amount
        B = balance of token before withdraw
        T = total supply
        s = shares to burn
        (T - s) / T = (B - a) / B 
        a = sB / T
        */
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        _burn(msg.sender, _shares);
        token.transfer(msg.sender, amount);
    }
}
```
3.Compile the Vault.sol contract.
4.Deploy the Vault contract, providing the address of the deployed ERC20 contract as the constructor parameter.
Interacting with the Contracts
1.Mint Tokens: Use the mint function of the ERC20 contract to create new tokens.
2.Approve Tokens: Use the approve function of the ERC20 contract to allow the Vault to transfer tokens on your behalf.
3.Deposit Tokens: Call the deposit function of the Vault contract to deposit tokens and receive shares.
4.Withdraw Tokens: Call the withdraw function of the Vault contract to redeem shares and receive tokens.
## Authors
Tanu Priya
## License
This project is licensed under the MIT License - see the LICENSE.md file for details
