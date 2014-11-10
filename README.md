# Auditor Guidelines

From the SELinux policies, the auditor can verify that manny-core has no access to postgresql or sensitive files.  manny-core can transmit log messages to syslog.  From `health_resource.erl`, the auditor can verify that the URL for reading system health (`/health`) is GET only.  From `admin_resource.erl`, the auditor can verify that POST is used (various `/admin` URLs).  The auditor can verify that manny-core uses the POSTS only to write client CRT and CRL files (which then get read by nginx when manny-core restarts nginx).
