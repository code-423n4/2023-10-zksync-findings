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
