# **Possum Labs (Portals) Audit Competition on Hats.finance** 


## Introduction to Hats.finance


Hats.finance builds autonomous security infrastructure for integration with major DeFi protocols to secure users' assets. 
It aims to be the decentralized choice for Web3 security, offering proactive security mechanisms like decentralized audit competitions and bug bounties. 
The protocol facilitates audit competitions to quickly secure smart contracts by having auditors compete, thereby reducing auditing costs and accelerating submissions. 
This aligns with their mission of fostering a robust, secure, and scalable Web3 ecosystem through decentralized security solutions​.

## About Hats Audit Competition


Hats Audit Competitions offer a unique and decentralized approach to enhancing the security of web3 projects. Leveraging the large collective expertise of hundreds of skilled auditors, these competitions foster a proactive bug hunting environment to fortify projects before their launch. Unlike traditional security assessments, Hats Audit Competitions operate on a time-based and results-driven model, ensuring that only successful auditors are rewarded for their contributions. This pay-for-results ethos not only allocates budgets more efficiently by paying exclusively for identified vulnerabilities but also retains funds if no issues are discovered. With a streamlined evaluation process, Hats prioritizes quality over quantity by rewarding the first submitter of a vulnerability, thus eliminating duplicate efforts and attracting top talent in web3 auditing. The process embodies Hats Finance's commitment to reducing fees, maintaining project control, and promoting high-quality security assessments, setting a new standard for decentralized security in the web3 space​​.

## Possum Labs (Portals) Overview

Portals is a next level upfront fixed yield protocol. Fully immutable _ permissionless_ developed by Possum Labs.

## Competition Details


- Type: A public audit competition hosted by Possum Labs (Portals)
- Duration: 2 weeks
- Maximum Reward: $27,800
- Submissions: 80
- Total Payout: $25,020 distributed among 16 participants.

## Scope of Audit

All contracts in main/contracts and respective sub-directories are in-scope.

## High severity issues


- **Infinite PortalEnergy Increase through MaxLockDuration Update and Multiple 0-Token Unstakes**

  The GitHub issue tackles a bug in Possum-Labs' Portals contracts regarding PortalEnergy. The problem was found in the `_updateAccount` function where portalEnergy gets increased by `portalEnergyEarned + portalEnergyIncrease`. With discrepancies between `maxLockDuration` and `lastMaxLockDuration`, users could potentially inflate their `portalEnergy` indefinitely. 

This inflation could occur by continually updating `maxLockDuration`, followed by unstaking minuscule amounts of tokens, manipulating `portalEnergyIncrease` in the process, resulting in a continuous growth of the user's `portalEnergy`.

In a worst-case scenario, individuals could exploit this issue to amass virtually unlimited `portalEnergy`, which can be traded for Possum tokens. Consequently, this may drain all Possum tokens available in the contract. 

The proposed solution involves updating the `lastMaxLockDuration` to align with the current `maxLockDuration` within the `_updateAccount` function. After the report, the issue ended with a posted fix, confirming that the `lastMaxLockDuration` was correctly updated in the `getUpdateAccount`.


  **Link**: [Issue #5](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/5)


- **Exploitation Potential by Front+Back Running the Convert() Function in Portal Launching Process**

  The issue addresses a potential exploit in the portal functioning, where an exploiter's front and back running of the convert() function can lead to the PE to PSM price never increasing to a viable point for staking in Possum. The exploiter potentially does not need to stake anything major, merely a minimal amount to obtain an account in the system. The exploiter monopolizes the PSM amounts buy/sell, transferring almost all the PSM added by convert(), leaving the price of internal LP unchanged. The consistent execution of this exploit could result in stakers avoiding the portal investment, rendering the portal ineffective.  The discussed exploit doesn't cause grief to arbitrageurs who attain their expected profit. Instead, it affects the stakers whose gained energy never accrues value. The issue also presents an exploitation scenario demonstrating the same. Solutions proposed to solve the exploit include applying a user-based timelock on internal LP interactions which, if executed with a sufficient duration, can even prevent multi-block MEV. Other solutions proposed require further consideration of potential side effects.


  **Link**: [Issue #66](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/66)

## Medium severity issues


- **Unexpected Reverting in `forceUnstakeAll` due to Rounding Down Issue in `updateAccount`**

  The issue discusses a significant bug where the `forceUnstakeAll` function unexpectedly reverts due to a rounding down issue in the `updateAccount` function. This bug is also valid for users attempting to unstake all of their tokens using the regular `unstake` function. The problem stems from the fact that `maxStakeDebt` can be smaller than `balance * maxLockDuration` due to rounding down issues, leading to state where the line `portalEnergy = accounts[msg.sender].portalEnergy -= (balance * maxLockDuration) / SECONDS_PER_YEAR` underflows. This causes the `forceUnstakeAll` function to revert even when the user has sufficient tokens. Possible solutions discussed were reordering calculations or adjusting the `maxStakeDebt` calculation to avoid round downs. A fix was applied to the original code as per the latter suggestion. The severity level was discussed, while no funds are lost beyond negligible residuals, core functionalities were affected thus leaning towards a higher severity issue.


  **Link**: [Issue #7](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/7)


- **Issue with Claiming Reward Token on Edge Case When Reward Token Matches the Principal Token**

  The issue is about an edge case where reward tokens can't be claimed if the reward token matches the principal token. A section of code in the `Compounder` contract, specifically the `convert()` function, was identified as the source of the issue. Stake() invocation presents another challenge: `_depositToYieldSource()` re-deposits the principal/reward token to `HLP_STAKING`, leaving minimal reward token in the contract for profitable conversion if the principal token is the main reward token. The recommendation is to verify future deployments to confirm that none of the reward tokens match the principal token. Additionally, it’s suggested to create a precautionary storage variable array of expected reward tokens to prevent deployment if any match `_PRINCIPAL_TOKEN_ADDRESS` in the constructor. There is a proposed edit in the `convert()` function and a suggestion to support native ETH handling.+


  **Link**: [Issue #39](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/39)


- **Portal Misprices Yield Due to Incorrect Token Price Ratio Calculation**

  The issue raised concerns the portal's current implementation, which fails to consider the price ratio between PSM and the principal token when calculating portal energy. In instances where PSM's price is less than the principal token, the yield returned may be significantly less than the staked amount. This shortfall can lead to a discrepancy between the staked sum and the yield obtained, which may result in users receiving lower returns than expected. In the issue's discussion thread, it is suggested that this problem could be resolved by taking into account both the principal token price and PSM price when utilizing the _FUNDING_EXCHANGE_RATIO. A revision of the current code is proposed, but further investigation and adjustments may be necessary.


  **Link**: [Issue #49](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/49)


- **Issue with Contract Not Receiving ETH as Yield Resulting in Potential Losses for Funders**

  The issue is about an identified edge case where the currently implemented contract does not have a function to receive yield as ETH. This gap in the functionality is preventing yield sources from sending ETH to the contract, which in turn leads to arbitrageurs refraining from using the convert function. The resultant effect is a direct loss of funds for funders. The reporter proposes adding a `receive()` function to remedy this situation. A contributor also suggested the addition of a fallback function to handle scenarios where `msg.data` is not empty. The reporter acknowledges and appreciates the constructive follow-up.


  **Link**: [Issue #69](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/69)

## Low severity issues


- **ERC20 Tokens Failing with Non-Zero Allowance Value in Stake Function**

  The issue pertains to some ERC20 tokens (like USDT) failing when changing allowance from a non-zero value. Particularly, the `stake()` function will fail with such tokens, as it tries an `approve()` operation that reverts if there's an existing approval. The problem lies in the `_depositToYieldSource()` in the `contracts/Portal.sol`. The recommended solution is to set the allowance to zero before increasing it, or by using `safeApprove()`.


  **Link**: [Issue #1](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/1)


- **Incorrect Calculation in getUpdateAccount View Function in quoteforceUnstakeAll**

  The getUpdateAccount view function in quoteforceUnstakeAll returns an inaccurate maxStakeDebt calculation, potentially leading to faulty data for protocols integrating with this contract. Proposed solution includes replacing the current calculation with portalEnergyIncrease for accurate results, thus maintaining protocol integrity during external interactions. The comment section acknowledges the finding.


  **Link**: [Issue #11](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/11)


- **Unsynchronized Variable Updates Allow Unbalanced Unstaking in Portal.sol**

  The issue revolves around a vulnerability in `Portal.sol` where an attacker stakes a token and then repeatedly calls the unstake function with a small amount parameter. This results in an inconsistency between four key global variables, allowing the user to conserve `maxStakeDebt` and `portalEnergy` while unstaking all their tokens. A proof of concept and revised code were provided as attachments.


  **Link**: [Issue #21](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/21)


- **Potential Drain of PSM Tokens in Portal Contract Due to Reserve0 Manipulation**

  The issue reports a potential vulnerability in a Portal contract concerning the PSM token. If reserve0 can be manipulated to exceed the constant product, it can trigger a scenario in which all PSM tokens could be drained from the contract by a malicious actor using a minimum amount of portalEnergy. Although risk assessment indicated the probability of such an event is low, it was suggested that sanity checks can help avert such attacks in the future.


  **Link**: [Issue #40](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/40)


- **Decimal Multiplication in Code Can Cause Phantom Overflows: Use Uniswap FullMath Library**

  The issue relates to potential phantom overflows when two numbers with decimals are multiplied in the code. This can happen in multiple instances in the code, as outlined in the provided links. The proposed recommendation suggests using the Uniswap FullMath library to avoid this, as it allows for multiplication and division that can overflow an intermediate value without losing precision.


  **Link**: [Issue #42](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/42)


- **Manipulation of portalEnergyEarned Calculation in _updateAccount Function Issue**

  In the _updateAccount function, portalEnergyEarned calculation is possibly exposed to manipulation by malicious users who's staked balance is lesser than SECONDS_PER_YEAR. By repeatedly calling burnPortalEnergyToken with a small amount and a victim user as the recipient, these users can avoid victims from obtaining portalEnergyEarned. Some users suggest removing the unnecessary account update in the burnPortalEnergyToken function to counteract this vulnerability.


  **Link**: [Issue #47](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/47)


- **Portal Implementation Overlooks Tokens with Fewer than 18 Decimals Affecting Energy Calculation**

  The issue pertains to the current implementation of the portal which does not properly handle tokens with fewer than 18 decimals (like USDC, USDT, and Wrapped BTC), causing calculation discrepancies. This issue affects the function allocating energy to users. It particularly impacts users of portals with less than 18 decimals as they gain much less yield. Solutions have been proposed to adjust calculations according to the token's decimals, and the issue has been fixed subsequently.


  **Link**: [Issue #48](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/48)


- **PortalEnergy Quote Functions Issue with Unset constantProduct and Misleading Values**

  The `portalEnergy` quote functions don't take into account when `constantProduct` hasn't yet been set, only obtaining a non-zero value after `activatePortal()`. This can result in providing a misleading value when user calls the function before the active phase. A proposed solution is the calculation of `_constantProduct` in memory before the funding phase in the quote functions without altering the storage variable `constantProduct`. The issue is viewed as low severity since, realistically, steps leading up to it cannot happen.


  **Link**: [Issue #63](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/63)


- **Incorrect Unstake Function Calculation Leading to Inaccurate Values**

  The issue pertains to an incorrect formula used for calculating 'availableToWithdraw' in the unstake function of a contract, which could result in inaccurate values. The user suggested a corrected formula and provided a proof of concept for testing purposes. Post the suggested revision, the tests passed, validating the proposed fix. The issue is subsequently addressed, and the correct computation is now incorporated in the unstake function.


  **Link**: [Issue #78](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/78)


- **Incorrect Calculation of maxStakeDebt in Unstake Function Leading to Inaccurate Values**

  The issue pertains to inaccuracies in the calculation of maxStakeDebt in the unstake function. These can lead to incorrect values for maxStakeDebt and availableToWithdraw. A proof of concept was provided which demonstrated the vulnerability, along with a potential fix to the problem - replacing the incorrect calculation with the corrected formula. The corrected code was included in the description.


  **Link**: [Issue #79](https://github.com/hats-finance/Possum-Labs--Portals--0xed8965d49b8aeca763447d56e6da7f4e0506b2d3/issues/79)



## Conclusion

The audit of Possum Labs (Portals) hosted by Hats.finance encompassed a competition running for two weeks, with 80 submissions resulting in a total payout of $25,020 among 16 participants. The audit surfaced several high, medium, and low severity issues relating to smart contract security. High severity issues included the potential for infinite PortalEnergy increase which could exploit all Possum tokens in the contract, and the potential exploitation of the Convert() function. Medium severity issues included unexpected reverting in forceUnstakeAll and the inability to claim reward tokens if the reward matches the principal token in an edge case. Low severity issues included ERC20 tokens failing with a non-zero allowance in the Stake function. All issues were discussed with contributors who suggested mitigation measures to increase the overall security of the DeFi protocol.

## Disclaimer


This report does not assert that the audited contracts are completely secure. Continuous review and comprehensive testing are advised before deploying critical smart contracts./n/n
The Possum Labs (Portals) audit competition illustrates the collaborative effort in identifying and rectifying potential vulnerabilities, enhancing the overall security and functionality of the platform.


Hats.finance does not provide any guarantee or warranty regarding the security of this project. All smart contract software should be used at the sole risk and responsibility of users.

