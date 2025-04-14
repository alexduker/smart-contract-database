# smart-contract-database
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract SmartContractDatabase {
    struct ContractInfo {
        string name;
        address contractAddress;
        string description;
        address owner;
    }

    mapping(address => ContractInfo) public contracts;
    address[] public contractList;
    
    event ContractAdded(string name, address indexed contractAddress, address indexed owner);
    event ContractRemoved(address indexed contractAddress);

    function addContract(string memory _name, address _contractAddress, string memory _description) public {
        require(contracts[_contractAddress].contractAddress == address(0), "Contract already exists");
        
        contracts[_contractAddress] = ContractInfo({
            name: _name,
            contractAddress: _contractAddress,
            description: _description,
            owner: msg.sender
        });
        contractList.push(_contractAddress);
        
        emit ContractAdded(_name, _contractAddress, msg.sender);
    }
    
    function removeContract(address _contractAddress) public {
        require(contracts[_contractAddress].owner == msg.sender, "Only owner can remove contract");
        delete contracts[_contractAddress];
        
        for (uint i = 0; i < contractList.length; i++) {
            if (contractList[i] == _contractAddress) {
                contractList[i] = contractList[contractList.length - 1];
                contractList.pop();
                break;
            }
        }
        
        emit ContractRemoved(_contractAddress);
    }
    
    function getContract(address _contractAddress) public view returns (ContractInfo memory) {
        require(contracts[_contractAddress].contractAddress != address(0), "Contract not found");
        return contracts[_contractAddress];
    }
    
    function getAllContracts() public view returns (address[] memory) {
        return contractList;
    }
}






