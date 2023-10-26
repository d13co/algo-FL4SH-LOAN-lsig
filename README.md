# Algorand FL4SH LOAN Logic Sig

Flash loan provider powered by a logic sig.

The logic sig nature enables (outer) transaction fee funding without pre-existing funds on-chain.

## Rules of Engagement

The FL4SH LOAN logic sig can be used to withdraw funds on the following conditions:

- First transaction of the group:
  - payment from the `FL4SHLOAN..` account
  - no rekeying or close-remainder-to
  - loan amount: amount paid + transaction fee
    - min paid amount: 0
    - transaction fee: can be any amount
    - max loan amount: (balance of flash loan account) minus 0.1 $ALGO (for MBR requirement)
- Last transaction of the group:
  - **outer** payment to the `FL4SHLOAN..` account
  - amount must be at least "Loan amount" from first transaction plus 10% interest
- In between: No restrictions

The loan amount can be 0, with only the transaction fee being "withdrawn". Any fee can be used, so you can fund a large transaction group, including inner transaction fee budget, so long as you repay it at the final transaction in the group. 

## Why would I use it?

It enables funding outer transaction fees to interact with smart contracts without having funds on-chain.

You could combine this with smart contracts to onboard people:

- e.g. in meetups/conferences: if you have a smart contract that validates some unique token before funding a new account
- e.g. in puzzle/quiz scenarios: if you have a smart contract that will release a prize in ALGO if the correct answer is given

In both cases you could use the `FL4SHLOAN` provider to enable users without funds on-chain to interact with your contracts.

You could get a microloan for transaction fees by using an atomic group like so:

- Take out a loan from the FL4SH LOAN account:
  - Amount: 0 $ALGO
  - Txn fees: 0.003 $ALGO (covers 3 minimum txn fees)
- Call your contract with the correct authorizing token/params/quiz answer/magic words
  - Your contract issues inner transaction, funding user with some ALGO *
- User repays the flash loan at 10% interest (0.0033 $ALGO)

_\* Your contract pays for the inner transaction fee in this scenario._

Tip: You usually won't need to take out the user's 0.1 $ALGO minimum balance requirement from `FL4SHLOAN`. Since the repayment has to come from somewhere, it makes sense that your smart contract will cover the user's MBR to avoid overpaying interest. A user with 0 ALGO balance can call your contract, so long as the transaction fees are taken care of on the outer layer, and their resulting balance after the repayment is over the minimum balance requirement.

## Javascript SDK

There is a rudimentary Javascript SDK you can use:

[algo-FL4SH-LOAN-sdk](https://github.com/d13co/algo-FL4SH-LOAN-sdk)

## Where is it deployed? How much is there to borrow?

The initial funding will be 1.3 $ALGO.

This is deployed to Algorand [mainnet](https://algoexplorer.io/address/FL4SHLOANLERAK6AFTBSBIL2WIWCRUFY7XWQK57H3EXM6GMMGMPT7C3UKI), [testnet](https://testnet.algoexplorer.io/address/FL4SHLOANLERAK6AFTBSBIL2WIWCRUFY7XWQK57H3EXM6GMMGMPT7C3UKI) and [betanet](https://betanet.algoexplorer.io/address/FL4SHLOANLERAK6AFTBSBIL2WIWCRUFY7XWQK57H3EXM6GMMGMPT7C3UKI) with the address:

```
FL4SHLOANLERAK6AFTBSBIL2WIWCRUFY7XWQK57H3EXM6GMMGMPT7C3UKI
```

To use this in sandnet, just fund the address with some ALGO and sign with the SDK.

## FAQ

### Q: Can I repay using a smart contract inner transaction?

No, the repayment needs to be an outer transaction.

I was not able to figure out a way to validate sibling inner transactions from a logic sig. I realize this is a significant limitation but I simply can't see any opcodes that enable this.

### Q: 10% interest?? This is very expensive!

You should probably only use the FL4SH LOAN provider to cover transaction fees.

To access a larger flash loan at a more reasonable price, this can be used in combination with a contract based flash loan provider, e.g. [Folks Finance](https://docs.folks.finance/architecture/flash-loan).

### Q: Where is the code? I want to hack this

You can find the commented TEAL code in this repo under `algo-FL4SH-LOAN-lsig.teal`

### Q: Is there admin access to the FL4SH LOAN logic sig?

Yes, the admin address `DTHIRTEENNLSYGLSEXTXC6X4SVDWMFRCPAOAUCXWIXJRCVBWIIGLYARNQE` can issue any transaction from the logic sig.

### Q: Who?

[D13](https://d13.co/)
