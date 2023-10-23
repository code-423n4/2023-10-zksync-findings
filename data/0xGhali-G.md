Using `Ownable2Step` library without use its advantage 
 i found the `_transferOwnership` only used in project which mean `Ownable2Step` should be replaced with `Ownable` which its base contract to remove unused and unnecessary functions which deployed a many times inside

`Ownable2Step` used in `Governance`, `ValidatorTimelock`, `AllowList`