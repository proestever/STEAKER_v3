# STEAKER_v3

# StakingContract Readme

This repository contains the Solidity smart contract code for the StakingContract. The contract allows users to stake tokens for a specified duration and earn rewards based on the staking period.

## Contract Details

The StakingContract is an ERC-20 compatible staking contract with the following details:

- **Name**: Staking Token
- **Symbol**: STK
- **Decimals**: 18

## Features

The contract includes the following features:

- Staking: Users can stake tokens by calling the `createStake` function, specifying the staking amount and duration.
- Unstaking: Users can unstake their tokens by calling the `endStake` function, providing the index of the stake to be ended.
- Balance: Users can check their token balance using the `balanceOf` function.
- Transfer: Users can transfer tokens to other addresses using the `transfer` and `transferFrom` functions.
- Approval: Users can approve other addresses to spend tokens on their behalf using the `approve` function.
- Allowance: Users can check the allowance granted to an address using the `allowance` function.
- Staking Details: Users can retrieve information about their stakes using the `getStakes` and `getTotalStakes` functions.

## Events

The contract emits the following events:

- `Transfer`: Triggered when tokens are transferred between addresses.
- `Approval`: Triggered when an address is approved to spend tokens on behalf of another address.
- `StakeStarted`: Triggered when a stake is created.
- `StakeEnded`: Triggered when a stake is ended.

## License

The contract is released under an UNLICENSED SPDX license, meaning it is not governed by any specific license.

## Security Considerations

It's important to review and audit the contract code before deploying it in a production environment. The code utilizes the OpenZeppelin library for SafeMath operations to prevent arithmetic overflows/underflows, which enhances the security of the contract. However, it is always recommended to perform thorough testing and security audits to ensure the contract's robustness and reliability.

Please note that this readme file provides an overview of the contract's functionality and usage. It does not cover all potential scenarios and edge cases. Therefore, it is essential to carefully analyze and understand the contract's code and its implications.
