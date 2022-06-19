# How did I create and deploy a new ERC 20 token in a single Solidity file

In this repo, I will show you how I created and deployed a new ERC 20 token in a single Solidity file. Yes, you're right, there is only one Solidity file *HXPT.sol* in this repo, and that's all you need.

A little bit about my background. Although I majored in engineering during my undergraduate, that degree was in civil engineering, so coding is still a little bit challenging for me. Motivated by my interests in blockchain, I searched for some easy ways to deploy ERC-20 tokens online, and the way I am showing below is the easiest way that I found.



## About this token

I call this token *Haydon's Experimental Token*. Its symbol is HXPT.

> Disclaimer: This ERC token is only intended for my practice purpose. It is never publicly or privately sold to anyone and does not constitute equity by any means. Furthermore, it is only deployed on Ropsten Test Network rather than Ethereum Mainnet, making HXPT not have any monetary value.

The source code of HXPT is as follows:

```
pragma solidity ^0.4.24;
 
//Safe Math Interface
 
contract SafeMath {
 
    function safeAdd(uint a, uint b) public pure returns (uint c) {
        c = a + b;
        require(c >= a);
    }
 
    function safeSub(uint a, uint b) public pure returns (uint c) {
        require(b <= a);
        c = a - b;
    }
 
    function safeMul(uint a, uint b) public pure returns (uint c) {
        c = a * b;
        require(a == 0 || c / a == b);
    }
 
    function safeDiv(uint a, uint b) public pure returns (uint c) {
        require(b > 0);
        c = a / b;
    }
}
 
 
//ERC Token Standard #20 Interface
 
contract ERC20Interface {
    function totalSupply() public constant returns (uint);
    function balanceOf(address tokenOwner) public constant returns (uint balance);
    function allowance(address tokenOwner, address spender) public constant returns (uint remaining);
    function transfer(address to, uint tokens) public returns (bool success);
    function approve(address spender, uint tokens) public returns (bool success);
    function transferFrom(address from, address to, uint tokens) public returns (bool success);
 
    event Transfer(address indexed from, address indexed to, uint tokens);
    event Approval(address indexed tokenOwner, address indexed spender, uint tokens);
}
 
 
//Contract function to receive approval and execute function in one call
 
contract ApproveAndCallFallBack {
    function receiveApproval(address from, uint256 tokens, address token, bytes data) public;
}
 
//Actual token contract
 
contract QKCToken is ERC20Interface, SafeMath {
    string public symbol;
    string public  name;
    uint8 public decimals;
    uint public _totalSupply;
 
    mapping(address => uint) balances;
    mapping(address => mapping(address => uint)) allowed;
 
    constructor() public {
        symbol = "HXPT";
        name = "Haydon's Experimental Token";
        decimals = 3;
        _totalSupply = 1000000;
        balances[0x675F7C826796fB4718dF083E73C50C752183fFd5] = _totalSupply;
        emit Transfer(address(0), 0x675F7C826796fB4718dF083E73C50C752183fFd5, _totalSupply);
    }
 
    function totalSupply() public constant returns (uint) {
        return _totalSupply  - balances[address(0)];
    }
 
    function balanceOf(address tokenOwner) public constant returns (uint balance) {
        return balances[tokenOwner];
    }
 
    function transfer(address to, uint tokens) public returns (bool success) {
        balances[msg.sender] = safeSub(balances[msg.sender], tokens);
        balances[to] = safeAdd(balances[to], tokens);
        emit Transfer(msg.sender, to, tokens);
        return true;
    }
 
    function approve(address spender, uint tokens) public returns (bool success) {
        allowed[msg.sender][spender] = tokens;
        emit Approval(msg.sender, spender, tokens);
        return true;
    }
 
    function transferFrom(address from, address to, uint tokens) public returns (bool success) {
        balances[from] = safeSub(balances[from], tokens);
        allowed[from][msg.sender] = safeSub(allowed[from][msg.sender], tokens);
        balances[to] = safeAdd(balances[to], tokens);
        emit Transfer(from, to, tokens);
        return true;
    }
 
    function allowance(address tokenOwner, address spender) public constant returns (uint remaining) {
        return allowed[tokenOwner][spender];
    }
 
    function approveAndCall(address spender, uint tokens, bytes data) public returns (bool success) {
        allowed[msg.sender][spender] = tokens;
        emit Approval(msg.sender, spender, tokens);
        ApproveAndCallFallBack(spender).receiveApproval(msg.sender, tokens, this, data);
        return true;
    }
 
    function () public payable {
        revert();
    }
}
```

You need to do is to save the code above to Ethereum Remix IDE as a .sol file and deploy it under injected Web3 environment. Remember to define your token name, symbol, and supplies in the code above.

Then I could see my HXPT tokens are delivered to my MetaMask wallet.

![sreenshot of my MetaMask wallet](/HXPT%20wallet%20screenshot.png)
