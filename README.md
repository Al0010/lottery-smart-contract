# Lottery Smart Contract
Creation of lottery-type smart contracts with decentralized prize pool and winner's random choice.

In this smart contract there will be two types of users: 

1) Manager: user who creates the lottery and has greater skills than the players. 
2) Player: a user who deposits ETH to participate in the lottery. 

The contract will have the ability to receive ETH from players. The winner is drawn by calling the `PickWinner` function by the manager. 

## Contract Definition 
Define the name of the contract, creating a variable 'manager' with a public address. We add a new variable 'players' showing all addresses of users participating in the lottery. 

`pragma solidity ^0.4.17;`

  `contract Lottery {`              
      `address public manager;`     
      `address[] public players;`
    
## Definition of the constructor 'Lottery'
Define the constructor with the same name as the contract which gives 'manager' to the user who deploys our contract. 

    function Lottery() public {
      manager = msg.sender;
    }

## Define the entry function 
Define a new function that will give the opportunity to participate in the lottery. The function is public, everyone can participate as long as the entry fee required by the 'manager' is deposited. 

    function enter() public payable {       
        require(msg.value > .0001 ether);    
        players.push(msg.sender);         
    }

## Create the random function with keccak256
Define a new function called 'random' which can only be read and returns a random number. The random number is drawn considering block difficulty, time and address in play. 

    function random() private view returns (uint) {  
        return uint(keccak256(block.difficulty, now, players));
    }

## Define the PickWinner function
Create a new public function to enable the drawing of the winner. The extraction can only be performed by the address 'manager'.
Define a variable "index" which stores the winner. Then the ETHs within the contract are taken and sent to the winner.
Once the lottery draw is over, the array is restored, allowing the lottery to be restarted without the need to deploy again. 
Finally, create a variable to return the ETH paid by each player if the lottery is suspended.
                                            
    function pickWinner() public restricted {  
        //require(msg.sender == manager);         
        uint index = random() % players.length;                                           
        players[index].transfer(this.balance);                       
        players = new address[](0);             
    }
    
        modifier restricted() {
        require(msg.sender == manager); 
        _;                              
    }

## Define the visibility function of players
Declare a public function that can be called by everyone and allows the players participating in the lottery to be displayed. 

    function getPlayers() public view returns (address[]) {
        return players;
    }    
