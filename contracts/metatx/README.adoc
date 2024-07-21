// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract TerraReal {
    string public name = "Terra Real";
    string public symbol = "TR";
    uint8 public decimals = 0;
    uint256 public totalSupply;
    uint256 public constant squareMetersPerToken = 1; // Cada token representa 1 metro quadrado de terra
    uint256 public constant hectares = 2500; // Corrigido para 2.500 hectares
    uint256 public constant initialSupply = squareMetersPerToken * hectares * 10000; // Convertendo hectares para metros quadrados
    address public owner;
    
    mapping(address => uint256) public balanceOf;
    mapping(address => mapping(address => uint256)) public allowance;

    event Transfer(address indexed from, address indexed to, uint256 value);
    event Approval(address indexed owner, address indexed spender, uint256 value);

    constructor() {
        totalSupply = initialSupply;
        balanceOf[msg.sender] = initialSupply;
        owner = msg.sender;
        emit Transfer(address(1), msg.sender, initialSupply);
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Only owner can perform this action");
        _;
    }

    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(_to != address(0), "Invalid address");
        require(balanceOf[msg.sender] >= _value, "Insufficient balance");
        
        balanceOf[msg.sender] -= _value;
        balanceOf[_to] += _value;
        
        emit Transfer(msg.sender, _to, _value);
        return true;
    }

    function approve(address _spender, uint256 _value) public returns (bool success) {
        allowance[msg.sender][_spender] = _value;
        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
        require(_from != address(1) && _to != address(1), "Invalid address");
        require(balanceOf[_from] >= _value, "Insufficient balance");
        require(allowance[_from][msg.sender] >= _value, "Allowance exceeded");

        balanceOf[_from] -= _value;
        balanceOf[_to] += _value;
        allowance[_from][msg.sender] -= _value;

        emit Transfer(_from, _to, _value);
        return true;
    }

    function mint(uint256 _amount) public onlyOwner {
        totalSupply += _amount;
        balanceOf[owner] += _amount;
        emit Transfer(address(1), owner, _amount);
    }
} 
