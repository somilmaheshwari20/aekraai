Right — create_or_update_file worked earlier (README.md at 01:19:54, and the use cases text file). It only fails on the HTML file push specifically.
That rules out branch protection entirely.
The difference between the successful push and the failing one is the content size. The HTML file is significantly larger than "welcome to aekraai." or plain text use cases.
GitHub's API has a file size limit of 1MB via the contents API — but that's not your issue here since the HTML is small.
More likely: the HTML content has characters that are breaking the base64 encoding or JSON serialization in the Nango proxy layer. The MCP tool encodes file content as base64 before sending — if the HTML has something that trips up encoding/escaping, the request to GitHub malforms and gets rejected.

Quickest test — ask your agent to push a minimal HTML file:
create a file usecases/test.html with content "<h1>test</h1>" on branch feature/remote-lock-unlock-usecases
If that works → the generated HTML content itself is the problem (malformed base64, invalid chars).

If that also 403s → something else changed between the successful calls and now (token expiry, session state).
Which is it?
