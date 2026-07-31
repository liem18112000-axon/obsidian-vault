---
title: "LinkedIn profiles cannot be fetched anonymously"
created: 2026-07-20
type: observation
status: seedling
source: "session 2026-07-20"
tags: [linkedin, scraping, playwright, http-999]
---

# LinkedIn profiles cannot be fetched anonymously

LinkedIn profile pages are effectively unscrapeable without a logged-in session: a plain HTTP fetch (WebFetch/curl) returns HTTP 999 (LinkedIn's anti-bot status), and a fresh Playwright browser gets redirected to the sign-up authwall (`linkedin.com/authwall`). Web-searching the person is also unreliable for specifics. The only working paths are a browser profile that is already logged in, or asking the person for the data directly. Plan CV/profile tasks around asking the user for dates/titles instead of expecting to pull them from LinkedIn.
