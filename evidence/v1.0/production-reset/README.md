# Production Reset Evidence

This directory contains evidence of the final AtlasBadge V1.0 production reset and clean-start validation.

Before the official launch, the evidence must demonstrate that:

* all test identities were removed from Firebase Authentication;
* all test data was removed from Cloud Firestore;
* Firebase Storage was empty;
* test usernames were no longer reserved;
* test public profiles were no longer accessible;
* no demonstration or preloaded data remained;
* cache or fallback mechanisms did not repopulate the backend;
* the product started with an empty state.

The final validation will also include:

1. creation of a disposable account;
2. execution of essential first-user smoke scenarios;
3. deletion of the disposable account;
4. verification of Authentication cleanup;
5. verification of Firestore cleanup;
6. verification of Storage cleanup;
7. verification that the username and public profile were removed;
8. confirmation that production returned to a completely empty state.

Any remaining test identity, document, file, username, profile or repopulated data blocks the official V1.0 launch.

Evidence in this directory must be sanitised before publication.

Passwords, API keys, tokens, private environment variables and other sensitive information must never be committed.
