# SampleWallet Contract

This project consists of two smart contracts: `Consumer` and `SampleWallet`. The `SampleWallet` contract implements a wallet that can manage allowances, guardian-based ownership changes, and transferring Ether with specific payloads.

## Table of Contents

- [Overview](#overview)
- [Contracts](#contracts)
  - [Consumer Contract](#consumer-contract)
  - [SampleWallet Contract](#samplewallet-contract)
- [Functions](#functions)
  - [Consumer Functions](#consumer-functions)
  - [SampleWallet Functions](#samplewallet-functions)
- [Usage](#usage)
- [Payload](#)


## Overview

The `SampleWallet` contract is designed to allow an owner to manage allowances for specific addresses, control Ether transfers, and propose new owners through a guardian system. The `Consumer` contract allows users to check the balance of the contract and deposit Ether into it.

## Contracts

### Consumer Contract

The `Consumer` contract provides basic functionality to check the balance of the contract and to deposit Ether.

#### Functions

- **`getBalance()`**
  - **Visibility**: `public`
  - **Returns**: `uint` - The current balance of the contract in Wei.
  - **Description**: This function returns the balance of the `Consumer` contract.

- **`deposit()`**
  - **Visibility**: `external`
  - **Modifiable**: `payable`
  - **Description**: This function allows users to deposit Ether into the `Consumer` contract.

### SampleWallet Contract

The `SampleWallet` contract implements various features for managing ownership and allowances.

#### State Variables

- **`address payable public owner`**
  - The current owner of the wallet.

- **`mapping(address => uint) public allowance`**
  - Tracks the allowance for each address that is allowed to send Ether.

- **`mapping(address => bool) public isAllowedToSend`**
  - Indicates whether a given address is allowed to send transactions.

- **`mapping(address => bool) public guardian`**
  - Stores addresses that are designated as guardians.

- **`address payable nextOwner`**
  - The address proposed to become the new owner.

- **`uint guardiansResetCount`**
  - A counter to track how many confirmations have been received from guardians for a proposed owner change.

- **`uint public constant confirmationsFromGuardiansForReset`**
  - The number of confirmations required from guardians to reset the owner (set to 3).

#### Functions

- **`constructor()`**
  - **Description**: Sets the initial owner of the wallet to the address that deployed the contract.

- **`proposeNewOwner(address payable newOwner)`**
  - **Visibility**: `public`
  - **Parameters**: `newOwner` - The proposed new owner address.
  - **Description**: Allows a guardian to propose a new owner. Requires confirmations from guardians to execute the change. If the proposed owner is different from the previous one, the reset count is reset.

- **`setAllowance(address _from, uint _amount)`**
  - **Visibility**: `public`
  - **Parameters**: 
    - `_from` - The address to which the allowance is being set.
    - `_amount` - The amount of allowance.
  - **Description**: Allows the owner to set an allowance for an address and mark it as allowed to send transactions.

- **`denySending(address _from)`**
  - **Visibility**: `public`
  - **Parameters**: `_from` - The address to deny sending permissions.
  - **Description**: Allows the owner to revoke sending permissions for a specific address.

- **`transfer(address payable _to, uint _amount, bytes memory payload)`**
  - **Visibility**: `public`
  - **Parameters**:
    - `_to` - The address to which the Ether will be sent.
    - `_amount` - The amount of Ether to transfer.
    - `payload` - Additional data to send along with the Ether transfer.
  - **Returns**: `bytes memory` - The return data from the called address.
  - **Description**: Handles Ether transfers. Only the owner can send any amount, while allowed addresses are limited to their specified allowances. The function calls the recipient address with the specified payload and requires that the call is successful.

- **`receive()`**
  - **Visibility**: `external`
  - **Modifiable**: `payable`
  - **Description**: Allows the contract to receive Ether directly.
 
  

## Payload 

In the `transfer` function of the `SampleWallet` contract, the `payload` parameter is used to pass additional data during the Ether transfer. This is particularly useful for interacting with other smart contracts that require specific function calls or parameters when receiving Ether.

### How Payload Works

1. **Calling Functions on Other Contracts**: 
   - The `payload` can contain function signatures and arguments that specify what the recipient contract should execute upon receiving Ether. This allows for more complex interactions in a single transaction.

2. **ABI Encoding**:
   - The `payload` is typically constructed using ABI encoding methods, which create a byte array that represents the function to be called and its parameters.
   - Example:
     ```solidity
     bytes memory payload = abi.encodeWithSignature("functionName(parameterType)", argument);
     ```

3. **Flexibility**:
   - By using `payload`, the `SampleWallet` contract can seamlessly interact with other contracts, allowing it to act as a multi-functional wallet that can not only transfer Ether but also trigger specific contract methods.

## Usage

1. Deploy the `SampleWallet` contract to the Ethereum network.
2. Use the `setAllowance` function to set allowances for addresses you want to grant permissions to send Ether.
3. Designate guardian addresses by setting their values in the `guardian` mapping.
4. Guardians can propose a new owner using the `proposeNewOwner` function.
5. Allow addresses to send Ether using the `transfer` function.



