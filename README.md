# Boss_yaqub-BASE-commit48
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;
contract SimpleStorage {

        //////////////////////////////////////////////
        /////   LISTS OF STATE VARIABLES    //////////
    ///////////////////////////////////////////////////
uint256 public FavoriteNumber;

struct Person {
    uint256 Favoritenumber;
    string Name;
}
Person[] public  ListOfPeopleFavoriteNumber;
mapping (string  => uint256 ) public NametoFavoriteNumber;
mapping (address  => uint256 ) public addressToAmountFunded;

address[] private Funders;
address private immutable Owner;
       /*//////////////////////////////////////////////////////////////
                              CONSTRUCTOR
    //////////////////////////////////////////////////////////////*/
constructor () {
    Owner = msg.sender;
}

                    /*//////////////////////////////////////////////////////////////
                              MODIFIERS
    //////////////////////////////////////////////////////////////*/
modifier OnlyOwner() {
    require(msg.sender == Owner, "you are not the Owner of this Contract!");
    _;
}
                /*//////////////////////////////////////////////////////////////
                            STORAGE LOGIC
    //////////////////////////////////////////////////////////////*/
function Store(uint256 _favoriteNumber) public {
    FavoriteNumber = _favoriteNumber;
}   

function Retrieve() external view returns (uint256) {
    return FavoriteNumber;
}

function Addperson (string memory _name,uint256 _myfavoriteNumber) external {
    ListOfPeopleFavoriteNumber.push(Person(_myfavoriteNumber, _name));
    NametoFavoriteNumber[_name] = _myfavoriteNumber;
}
function Fundme() external payable OnlyOwner {
 require(msg.value > 0, "Send ETH");
 if (addressToAmountFunded[msg.sender] == 0) {
    Funders.push(msg.sender);
 }
 addressToAmountFunded[msg.sender] += msg.value;

}
function Withdraw() external payable OnlyOwner  {
    // reset All Address to Zero (0)

for (uint i = 0; i < Funders.length; i++) {
    address funder = Funders[i];
            addressToAmountFunded[funder] = 0;
}
delete Funders;
            // transfer Balance to owner ////
            (bool succes,) = payable (Owner).call{value: address(this).balance} ("");
            require(succes, "Transfer Failed");
}
}
