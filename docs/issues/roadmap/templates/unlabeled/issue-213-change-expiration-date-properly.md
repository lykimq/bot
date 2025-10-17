# Issue #213: Change expiration date properly

**Priority**: 🚀 Low
**Difficulty**: 🟡 Medium

## Problem Description

I mistakenly wrote `55*10` instead of `60*8` since it's not 55 minutes instead of 60 but 8 minutes instead of 10    A safe way would be, if you want me to try it, to     > date && curl -I https://api.github.com | grep -Fi "date"    Read the two dates, compute the difference in time and create the ex...
