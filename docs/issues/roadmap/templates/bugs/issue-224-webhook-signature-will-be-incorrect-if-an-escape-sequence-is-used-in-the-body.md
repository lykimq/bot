# Issue #224: Webhook signature will be incorrect if an escape sequence is used in the body

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

I haven't investigated it thoroughly but if my body contains the escape sequence `^[`, it looks like Mirage_crypto is producing the wrong SHA1 and the bot fails because it thinks that the webhook is signed with a wrong signature.    What I find strange is that I'm sure I used it previously with no p...
