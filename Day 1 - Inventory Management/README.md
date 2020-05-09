# Day 1 - Inventory Management

Day One involved some cookie manipulation in order to log into an administrator account that had been locked out.

I ran three test cases to identify any similarities in authentication cookies

Only one cookie is set -  `authid`

|RAW COOKIE|user email|user username|user password|decoded cookie|
|:--:|:--:|:--:|:--:|:--:|
|aXRndXl2NGVyOWxsMSFzcw==|test@test.com|itguy|itguy|itguyv4er9ll1!ss|
|aXRndXkydjRlcjlsbDEhc3M=|test2@test.com|itguy2|itguy2|itguy2v4er9ll1!ss|
|bmV3dXNlcnY0ZXI5bGwxIXNz|newuser@new.net|newuser|newuser|newuserv4er9ll1!ss|


After looking at the decoded cookies (which were Base64 encoded), we see a similarity between all cookies - 
They appear to follow the format of `[username] + v4er9ll1!ss`

Therefore, in order to access the account of the target (mcinventory), we need to change our `authid` cookie to be of value `mcinventoryv4er9ll1!ss`.

When we encode this with Base64, we get `bWNpbnZlbnRvcnl2NGVyOWxsMSFzcw==`, which is correct, and authenticates us.