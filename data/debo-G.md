## [G-01] SPLITTING REQUIRE STATEMENTS
**Description**
Require statements when combined using operators in a single statement usually lead to a larger deployment gas cost.
**Remediation**
It is recommended to separate the require statements with one statement/validation per line.
**Locations**
**Bad**
```sol
// 2023-10-canto/blob/main/canto_ambient/contracts/mixins/MarketSequencer.sol#L319-L321
            require((bend.lowTick_ >= TickMath.MIN_TICK) &&
                    (bend.highTick_ <= TickMath.MAX_TICK) &&
                    (bend.lowTick_ <= bend.highTick_), "RT");
```
**Fix**
```sol
// 2023-10-canto/blob/main/canto_ambient/contracts/mixins/MarketSequencer.sol#L319-L321
            require(bend.lowTick_ >= TickMath.MIN_TICK, "RT");
            require(bend.highTick_ <= TickMath.MAX_TICK, "RT");
            require(bend.lowTick_ <= bend.highTick_, "RT");
```
## [G-02] ARRAY LENGTH CACHING
**Description**
During each iteration of the loop, reading the length of the array uses more gas than is necessary. 
In the most favourable scenario, in which the length is read from a memory variable, storing the array length in the stack can save about 3 gas per iteration. 
In the least favourable scenario, in which external calls are made during each iteration, the amount of gas wasted can be significant.
The following array was detected to be used inside loop without caching it's value in memory: weeksToClaim.
**Remediation**
Consider storing the array length of the variable before the loop and use the stored length instead of fetching it in each iteration.
**Locations**
```sol
// 2023-10-canto/blob/main/canto_ambient/contracts/mixins/LiquidityMining.sol#L174-L191
for (uint256 i; i < weeksToClaim.length; ++i) {

            uint32 week = weeksToClaim[i];

            require(week + WEEK < block.timestamp, "Week not over yet");

            require(

                !concLiquidityRewardsClaimed_[poolIdx][posKey][week],

                "Already claimed"

            );

            uint256 overallInRangeLiquidity = timeWeightedWeeklyGlobalConcLiquidity_[poolIdx][week];

            if (overallInRangeLiquidity > 0) {

                uint256 inRangeLiquidityOfPosition;

                for (int24 j = lowerTick + 10; j <= upperTick - 10; ++j) {

                    inRangeLiquidityOfPosition += timeWeightedWeeklyPositionInRangeConcLiquidity_[poolIdx][posKey][week][j];

                }

                // Percentage of this weeks overall in range liquidity that was provided by the user times the overall weekly rewards

                rewardsToSend += inRangeLiquidityOfPosition * concRewardPerWeek_[poolIdx][week] / overallInRangeLiquidity;
            }
            concLiquidityRewardsClaimed_[poolIdx][posKey][week] = true;

        }
```
```sol
// 2023-10-canto/blob/main/canto_ambient/contracts/mixins/LiquidityMining.sol#L266-L284
for (uint256 i; i < weeksToClaim.length; ++i) {

            uint32 week = weeksToClaim[i];

            require(week + WEEK < block.timestamp, "Week not over yet");

            require(

                !ambLiquidityRewardsClaimed_[poolIdx][posKey][week],

                "Already claimed"

            );

            uint256 overallTimeWeightedLiquidity = timeWeightedWeeklyGlobalAmbLiquidity_[

                    poolIdx

                ][week];

            if (overallTimeWeightedLiquidity > 0) {

                uint256 rewardsForWeek = (timeWeightedWeeklyPositionAmbLiquidity_[

                    poolIdx

                ][posKey][week] * ambRewardPerWeek_[poolIdx][week]) /

                    overallTimeWeightedLiquidity;

                rewardsToSend += rewardsForWeek;

            }

            ambLiquidityRewardsClaimed_[poolIdx][posKey][week] = true;

        }
```
## [G-03] CHEAPER INEQUALITIES IN IF()
**Description**
The contract was found to be doing comparisons using inequalities inside the if statement.
When inside the if statements, non-strict inequalities `(>=, <=)` are usually cheaper than the strict equalities `(>, <)`.
**Remediation**
It is recommended to go through the code logic, and, if possible, modify the strict inequalities with the non-strict ones to save `~3 gas` as long as the logic of the code is not affected.
**Locations**
**Bad**
```sol
// 2023-10-canto/blob/main/canto_ambient/contracts/mixins/LiquidityMining.sol#L192-L192
 if (rewardsToSend > 0) {
```
**Fix**
```sol
// 2023-10-canto/blob/main/canto_ambient/contracts/mixins/LiquidityMining.sol#L192-L192
 if (rewardsToSend >= 1) {
```
**Bad**
```sol
// 2023-10-canto/blob/main/canto_ambient/contracts/mixins/LiquidityMining.sol#L285-L285
if (rewardsToSend > 0) {
```
**Fix**
```sol
// 2023-10-canto/blob/main/canto_ambient/contracts/mixins/LiquidityMining.sol#L285-L285
if (rewardsToSend >= 1) {
```