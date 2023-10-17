## Any comments for the judge to contextualize your findings
List of vulnerabilities I found:
Burn.
Fallback.
Reentrancy.
Library.
Math.
I have found genuine reentrancy in past contest.
I'm also a certified warden.

## Approach taken in evaluating the codebase
Search for keywords vulnerabilities.
Write POC exploit and impact analysis.

## Architecture recommendations
Deposits could do with emitting events.
withdrawTo and bridgeBurn functions should use reentrancy guard.

## Codebase quality analysis
Use reentrancy guard.

## Centralisation risks
L2bridge can solely call the bridgeBurn function.
If the contract is compromised it can lead to loss of funds.

## Mechanism review
upgradeability implemented so patches are possible.

## Systemic risks
Upgradeable contracts have security risks.
In that the upgradeability makes the contract prone to potential theft of user funds.

### Time spent:
35 hours