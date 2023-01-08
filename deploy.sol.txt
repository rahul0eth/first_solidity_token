// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;


contract RahulToken {
    // Storage Variables
    address public admin;
    string public name;
    string public symbol;
    mapping(address=>uint256) public balanceOf;
    uint256 public totalSupply;
    mapping(address=>mapping(address=>uint256)) public allowance;

    // Events
    event Transfer(address from, address to, uint256 amount);
    event Approval(address owner, address spender, uint256 amount); 

    constructor(string memory _name, string memory _symbol) {
        admin = msg.sender;
        name = _name;
        symbol = _symbol;
        totalSupply += 1e24;
    }

    function transfer(address account, uint256 amount) public {
        uint256 userBalance = balanceOf[msg.sender];
        require(userBalance >= amount, "Insufficient balance.");

        balanceOf[msg.sender] = userBalance - amount;

        balanceOf[account] = amount;

        Transfer(msg.sender, account, amount);
    }

    function mint(uint256 amount) public adminOnly {
        balanceOf[msg.sender] += amount;
    }


    function approve(address account, uint256 amount) public {
        allowance[msg.sender][account] = amount;

        Approval(msg.sender, account, amount);
    }

    function transferFrom(address from, address to, uint256 amount) public {
        require(amount <= allowance[from][msg.sender], "Insufficient allowance.");

        balanceOf[from] -= amount;
        balanceOf[to] += amount;

        allowance[from][msg.sender] -= amount;
        
        Transfer(from, to, amount);
    }


    modifier adminOnly {
        require(msg.sender == admin, "Not the admin.");
        _;
    }
}