# Four-lane crawler source bundle

Download `curl_cffi_four_lanes_verified.zip` from this branch and extract it.
The archive contains Python source, requirements, instructions, tests, a SHA-256 manifest, and a verification report.

- Four configurable asyncio list pollers for 28Hse and House730 rent/buy.
- Session-specific UA/cookies, shared per-site concurrency, HTTP 403 stop and HTTP 429 cooldown.
- Optional capture of real requests from existing local BitBrowser/CDP tabs.
- 22 tests passed from the extracted archive, including a local Chromium/CDP fixture.

This is a Python source bundle. Read README_CRAWLER.md inside the archive for installation and startup.
Live website behavior has not been verified. Turnstile is not automatically solved.

SHA-256: f85a04aed69abb0d9e6a10b3e481992ccea1806521cf6881772d21b9feea2879
