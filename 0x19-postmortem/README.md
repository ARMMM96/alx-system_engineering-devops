# Postmortem: June 5, 2024, API Service Outage

## Issue Summary
**Duration:** June 5, 2024, 14:00 - 15:30 UTC  
**Impact:** The API service was completely down, affecting 100% of users. Users experienced failed API requests, resulting in application timeouts and inability to access the service.  
**Root Cause:** The outage was caused by an unhandled exception in the API server's request routing logic, which led to a cascading failure.

## Timeline
- **14:00 UTC** - Issue detected via automated monitoring alert indicating high error rates.
- **14:02 UTC** - On-call engineer received alert and confirmed the issue.
- **14:05 UTC** - Initial investigation into API server logs began, revealing a spike in 500 Internal Server Error responses.
- **14:15 UTC** - Assumed root cause to be a database connectivity issue. Checked database performance and logs, but found no anomalies.
- **14:25 UTC** - Noticed an unusual pattern in the logs suggesting a possible problem with request routing.
- **14:30 UTC** - Incident escalated to the backend engineering team for deeper investigation.
- **14:40 UTC** - Discovered a specific unhandled exception in the request routing code causing the API server to crash.
- **14:45 UTC** - Temporary fix applied to handle the exception and restart the API servers.
- **15:00 UTC** - Monitoring confirmed that error rates had returned to normal levels.
- **15:30 UTC** - Full service restored and post-incident analysis initiated.

## Root Cause and Resolution
**Root Cause:** The root cause of the outage was an unhandled exception in the request routing logic of the API server. A recent code deployment introduced a bug where certain malformed requests caused the routing function to throw an exception that was not caught, leading to the server crashing repeatedly under load.

**Resolution:** The immediate fix involved adding proper exception handling around the routing logic to ensure that malformed requests are gracefully handled and logged without crashing the server. The servers were then restarted to clear the error state and restore normal operation. A more comprehensive code review was initiated to prevent similar issues in the future.

## Corrective and Preventative Measures
**Improvements:**
- Enhance automated testing to include scenarios with malformed requests to catch such issues before deployment.
- Improve monitoring to detect patterns that could indicate routing issues specifically, not just generic high error rates.
- Conduct regular code reviews focused on exception handling and error logging practices.

**Tasks:**
1. **Patch the API server codebase:** Ensure all exceptions are properly handled and logged.
2. **Improve automated tests:** Add tests for malformed requests and other edge cases.
3. **Enhance monitoring:** Implement more granular alerts to detect specific types of failures such as routing exceptions.
4. **Conduct training:** Provide additional training for engineers on best practices for error handling and logging.
5. **Deploy canary releases:** Use canary deployments for new releases to catch issues in a subset of servers before a full rollout.

By addressing these measures, we aim to prevent similar outages in the future and ensure more robust handling of unexpected conditions in the API service.
