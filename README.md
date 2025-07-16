# Beginner's Guide to ERC-4626 Standard

## Introduction to ERC-4626

Imagine you could deposit cryptocurrency into a digital vault and receive tokens representing your share of the vault's total assets. This is exactly what ERC-4626 enables on the Ethereum blockchain. Officially known as the "Tokenized Vault Standard", ERC-4626 provides a unified framework for creating vaults that accept ERC-20 tokens and issues corresponding share tokens to depositors.

## Why ERC-4626 ?

Tokenized vaults are widely used in decentralized finance (DeFi) projects, including lending platforms, yield strategies, and asset management. However, without a standardized approach, each project implements vaults differently.

This lack of uniformity creates challenges for applications like yield aggregators or portfolio managers that need to interact with multiple vaults. Developers must write custom integration logic for each implementation, increasing development time, introducing potential vulnerabilities, and duplicating effort across projects.

ERC-4626 solves this problem by establishing a standard for tokenized vaults. By defining a consistent interface for deposits, withdrawals, and share calculations, it ensures interoperability between vaults and third-party applications. This standardization reduces integration complexity, minimizes errors, and accelerates innovation by allowing developers to build on a shared foundation.

## Prerequisites

1. Specification

- Mandatory EIP-20 Implementation

  All vaults must implement the EIP-20 standard for `share` tokens. This includes:

  - Basic functions: `balanceOf`, `transfer`, `totalSupply`,etc operate on the Vault “shares” which represent a claim to ownership on a fraction of the Vault’s underlying holdings.
  - Metadata extensions: The `name` and `symbol` functions should reflect the underlying token’s `name` and `symbol`.
  - Non-transferrable vaults may revert on calls to `transfer` or `transferFrom`.

- Optional EIP-2612 Support

  EIP-4626 tokenized Vaults may implement EIP-2612 to improve the UX of approving shares on various integrations.

2. Key Terminologies

   | Term                     | Definition                                                                                                                                            |
   | ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Yield-bearing Vaults** | Smart contracts designed to optimize the returns on cryptocurrency assets by pooling and strategically allocating them across various DeFi protocols. |
   | **Asset**                | The underlying token of the vault with units managed by the corresponding ERC-20 contract.                                                            |
   | **Share**                | Generated tokens exchanged for tokens that were originally locked in the vaults.                                                                      |
   | **Fee**                  | Charges applied to operations (deposits, withdrawals, AUM, or yield) to the user by the vault.                                                        |
   | **Slippage**             | Difference between expected and actual share pricing during transactions excluding fees                                                               |

## ERC-4626 Methods

The table below outlines the key functions defined in the ERC-4626, with an explanation of the purpose and return value of each function.

| Method                                                                                       | Purpose                                                                                                                  |
| -------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **asset()**                                                                                  | Returns the address of the underlying token used for the Vault for accounting, depositing, and withdrawing.              |
| **totalAssets()**                                                                            | Returns the total amount of the underlying asset that is managed by Vault.                                               |
| **convertToShares()**/<br>**convertToAssets()**                                              | Returns the amount of shares/assets that the Vault would exchange for the amount of assets/shares provided respectively. |
| **previewDeposit()**/<br>**previewWithdraw()**/<br>**previewMint()**/<br>**previewRedeem()** | Simulates the effects of a deposit/withdrawal/mint/redemption at the current block (with current on-chain conditions).   |
| **deposit()**                                                                                | Mints Vault shares to receiver by depositing exact amount of underlying tokens.                                          |
| **maxDeposit()**                                                                             | Maximum amount of the underlying asset that can be deposited into the Vault for the receiver.                            |
| **mint()**                                                                                   | Mints exactly specified shares to receiver by depositing assets of underlying tokens.                                    |
| **maxMint()**                                                                                | Returns the maximum amount of shares that can be minted from the vault for the receiver.                                 |
| **withdraw()**                                                                               | Burns shares from owner and sends exact assets of underlying tokens to receiver.                                         |
| **maxWithdraw()**                                                                            | Returns the maximum amount of the underlying asset that can be withdrawn from the owner's balance.                       |
| **redeem()**                                                                                 | Burns exactly specified shares from owner and sends assets of underlying tokens to receiver.                             |
| **maxRedeem()**                                                                              | Returns the maximum amount of vault shares that can be redeemed from the owner's balance.                                |

## ERC-4626 Events

Two key events `Deposit` and `Withdraw` capture these state transitions:

- **Deposit**: Emitted when a user deposits underlying assets into the Vault, receiving an equivalent amount of shares in return. The `sender` initiates the transaction, and the `owner` receives the shares.

- **Withdraw**: Emitted when shares are redeemed or withdrawn from the Vault. The `sender` triggers the withdrawal, converting the `owner`’s shares back into assets, which are then transferred to the `receiver`.

The following state diagram illustrates the flow of asset and share transfers within an ERC-4626-compliant Vault.

```mermaid
sequenceDiagram
    participant U as User
    participant V as ERC4626
    participant A as ERC20 Asset

    U->>V: deposit(assets, receiver)
    V-->>A: transferFrom(user, vault, assets)
    V->>V: _mint(receiver, shares)
    V->>V: emit Deposit(sender, owner, assets, shares)
    V->>V: afterDeposit(assets, shares)
    V->>U: Return shares

    U->>V: withdraw(assets, receiver, owner)

    V->>V: beforeWithdraw(assets, shares)
    V->>V: _burn(owner, shares)
    V->>V: emit Withdraw(sender, receiver, owner, assets, shares)
    V-->>A: transfer(receiver, assets)
    A->>U: Send assets
```

## Further Reading

- [EIP-4626 Proposal](https://eips.ethereum.org/EIPS/eip-4626)
- [EIP-4626 Github Repo](https://github.com/transmissions11/solmate/blob/main/src/tokens/ERC4626.sol)

## Conclusion

By standardizing the logic, methods, and key token behaviors of yield-bearing vaults, ERC-4626 addresses critical gaps in interoperability and transparency across DeFi protocols. This standardization enables developers to integrate yield strategies more easily into decentralized applications, and improving efficiency across the DeFi ecosystem.
