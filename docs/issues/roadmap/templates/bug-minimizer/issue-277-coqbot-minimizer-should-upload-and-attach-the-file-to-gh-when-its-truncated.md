# Issue #277: coqbot minimizer should upload and attach the file to GH when it's truncated

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

We can probably use https://github.com/actions/upload-artifact/issues/50 / https://docs.github.com/en/free-pro-team@latest/rest/reference/actions#download-an-artifact to get a link to the uploaded artifact(s) from the GH Actions job, and include the URL in the data we send to the bot.
