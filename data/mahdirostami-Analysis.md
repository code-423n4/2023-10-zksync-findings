## Any comments for the judge to contextualize your findings
there is no comment, I try my best to review permissions, I try my best for both users and the protocol(operator) considering user loss and operator loss, considering different scenarios and attack vectors
### Approach taken in evaluating the codebase
First I try to understand the whole protocol, after that I go through different parts such as : L1, L2, and the System contract
after that, I go throw each path L1 To L2, L2 To L1
after that, I review each layer in different parts:
bridges, governance, facets.
I focus more on funds and Access control and permissions
then I try to change tests and review the weaknesses in tests.
### Codebase quality analysis
the code was really god documented and making it in different parts was a great idea, The docs in files are good, but I noticed some lack of docs in circuit files.
### Centralization risks
As there are two parts (owner) and (security council) that have separate functionalities both are multi-sig and both could cancel an operation, there is no direct risk from Centralization.
And the protocol focuses on neither governance nor the security council should be able to circumvent the limitations imposed on them, so it's great. 
There is no direct risk for users.
Bootloader couldn't bypass any user's request, so there is no Centralization risks, both refund gas and over head cost are verified and there is no permission to cost more for user.
But there are some risk related to allow list which I mentioned in my submission and here,
it's ok to not allow some users to deposit or call other function but if the user's fund are at the process, so the user must have to call some other function to get their funds back.
For example, if a user could call withdraw in l2, the user must have a permission to call finalize in l1.
### Total Hours Spent:
I dedicated approximately 50 hours to this review.

### Time spent:
50 hours