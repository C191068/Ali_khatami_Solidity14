# Ali_khatami_Solidity14
### Advanced Solidity Immutable and Constant 
![a1](https://github.com/C191068/Ali_khatami_Solidity14/assets/89090776/6e331a1f-a202-4834-9722-e244461d533c)
Figure1: execution cost of this ```akrkFundMe.sol``` contract created at previos repository is about ```794917 gas```

To decrease this cost we gonna learn two keywords one is ```constant``` keyword nad another is ```immutable``` keyword <br>

if you assing a variable outside of a function and never change it then use ```constant``` keyword 

we made a change in the code below

```

//SPDX-License-Identifier:MIT

pragma solidity ^0.8.8;

import "./akrkPriceConvertor.sol";

contract akrkFundMe  {

    using akrkPriceConverter for uint256;
    //if you assing a variable outside of a function and never change it then use constant keyword like below

    uint256 public  constant  minimumUSD=50 * 1e18; //as getConversionRate() returns 18 zeroes after decimel place
 
    address[] public funders;// we create an address array make it public 
// we have use payable keyword with the function below to make it payable with any native blockchain currency
    //below we have done mapping of addresses to how much money each of the people sent
    mapping(address => uint256) public addressToAmountFunded ;


    address public owner;// this is a global variable



    //the constructor below will set up as the owner of the contract

     constructor(){

         owner= msg.sender; // owner is equal to msg.sender

     }


    function fund() public payable {
     
     //as we want to convert msg.value to USD we made the following changes
       
        require(msg.value.getConversionRate() >= minimumUSD , "Not send enough");// to get accees to the 'value' at deploy at run transaction tab
        //1e18 is 1 ether, here the value must be greater than 1 ether
        //require is a checker it checks whether the value is greater than 1 ether
        // if not it is going to revert with an error messsage shown above
        //adding funders to the array 
         funders.push(msg.sender);
         addressToAmountFunded[msg.sender] = msg.value;// It is for when somebody funds our contract

    }// To send money. We made it public so that anybody can call it

  //The function below is created so that we can get price of ethereum in terms of USD , so that we can convert msg.value to USD
//Both functions are public because we can do whatever we want with them
//By using the getPrice() function below we are going to interact with contract outside of our project
  

  //below we will create a withdraw function

  //we use modifier in the function below

  function withdraw() public onlyOwner{

      

      for(uint256 funderIndex=0; funderIndex < funders.length; funderIndex++){

          address funder = funders[funderIndex]; //to access the first element or 0th element and gonna return an address for us to use
          // we gonna use the above line to reset our mapping 
          addressToAmountFunded[funder]=0;
      }

      // reset the array
      //here below the funders array is now equal to brand new address array with zero object to start with
      // if 1 in the first bracket then there will be one element to start with
      funders= new address[](0);
      // if we want to transfer fund to whomever is calling the withdrawal function we wil do the 
      //following
     // msg.sender.transfer(address(this).balance); //here 'this' refers to the whole 'akrkFundMe' contract
      // by using .balance we can get ethereum blockchain currency of this contract address 

      

      //call is the recommended as it forwards all gas and it has no limit and bool like sender

      (bool callSuccess, )= payable(msg.sender).call{value: address(this).balance}("");//here inside the bracket of call we gonna call function but now it is kept blank
     // here it gonna returned two variables but we need only one varaible
     require(callSuccess, "Call failed");


     
}


modifier onlyOwner {
         require(msg.sender == owner, "Sender is not owner!");// to check is msg.sender is equal to owner
         _;
     }

  
   
    

}


```

![a2](https://github.com/C191068/Ali_khatami_Solidity14/assets/89090776/f0a28b18-12c5-41a7-be01-55be3f1c1983)
figure2:execution cost of this akrkFundMe.sol contract created at previos repository is now about 775327 gas<br>
which has decreased about 19,590<br>

![a1](https://github.com/C191068/Ali_khatami_Solidity14/assets/89090776/beb2d224-78d5-4732-a7ed-37a2bae7d552)

Figure3: If we remove the constant keyword and click the ```MINIMUM_USD``` button at the deployed contract <br>
at console we see the execution cost is ```2451 gas(Cost only applies when called by a contract)```

![a2](https://github.com/C191068/Ali_khatami_Solidity14/assets/89090776/39a0797b-fb7e-4559-b54c-f884b2f1125e)
Figure4: after we add constant keyword the exexcutioncost is ```351 gas (Cost only applies when called by a contract)```










