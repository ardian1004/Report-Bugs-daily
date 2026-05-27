Service Unavailable During Generate EskaDB Mobile Despite Successful Backend Processing
Project Overview
Item	Detail
Application	EskaLink Mobile – Generate EskaDB Module
Testing Type	Functional Testing, API/Network Testing, Backend Validation, Error Handling Testing
Environment	Production / Staging
OS	Windows 11
Browser	Google Chrome
Tools Used	Chrome DevTools, Network Inspector
Issue Summary

During the Generate EskaDB Mobile process, the backend successfully completed all processing activities, including file generation and upload. However, the frontend displayed a popup error:

Service Unavailable

Investigation showed that:

Backend processing completed successfully
Data generation and upload were successful
Frontend received an HTTP 503 Service Unavailable
The issue occurred because the execution time exceeded the configured PHP server timeout limit
Severity & Priority
Category	Level
Severity	High
Priority	High
Reason

Although the backend process completed successfully, users perceived the process as failed because the UI displayed an error message.

Potential Impact
Misleading information for users
Risk of duplicate execution due to repeated retries
Reduced user trust in the system
Preconditions

Before reproducing the issue:

User is logged in to EskaLink
User has access to the Generate EskaDB feature
Server is running normally
Steps to Reproduce
Login to the EskaLink application
Navigate to the Generate EskaDB menu
Start the mobile database generation process
Wait for the process to complete
Observe the response shown in the UI
Expected Result

After backend processing is completed:

UI should display a SUCCESS status
No error popup should appear
User should receive a successful completion notification
Actual Result

Frontend displayed the following popup:

Service Unavailable

However, backend validation confirmed:

File generation completed successfully
Upload process completed successfully
Backend logs showed process status as SUCCESS
Evidence & Findings
UI Error Observation

The application displayed:

Service Unavailable

Additional Findings
Backend logs confirmed successful execution
Upload logs showed successful completion
Network Inspection Result

Using:

Chrome DevTools → Network → Fetch/XHR

Findings
Item	Result
HTTP Status	503 Service Unavailable
Request Type	XHR Request Failed
Endpoint	jobs_generate
Technical Investigation
Initial Analysis

The backend process required a relatively long execution time.

As a result:

The backend continued processing normally
Frontend request timed out before receiving the final response
Troubleshooting Performed
1. Controller Timeout Adjustment

File:
AutoGenDataController.php

Action:
Implemented:

set_time_limit(...)
Result

Issue still occurred.

2. Network Inspection

Using Chrome DevTools Network Inspector.

Findings
HTTP 503 Service Unavailable
3. Root Cause Analysis

The root cause was identified as:

PHP max_execution_time configuration on the server was too low for the processing duration required by Generate EskaDB.

Because of this:

Backend processing continued running
Frontend timed out earlier and received HTTP 503
Final Resolution

Server-side PHP configuration was updated:

max_execution_time = <higher value>
After Configuration Adjustment
Generate EskaDB process executed normally
Frontend successfully received response
No more “Service Unavailable” popup appeared
User experience returned to normal
Root Cause

Server-side PHP execution timeout caused the frontend request to terminate early and return HTTP 503 before backend processing completed.

QA Analysis
Impact Analysis
Impact Area	Description
User Experience	Users assumed the process failed
System Reliability	Potential duplicate retries by users
Operational Risk	Risk of repeated database generation
Recommendations
Short-Term Improvements
Increase PHP execution timeout
Improve frontend error handling
Display clearer processing status to users
Long-Term Improvements
Recommended Enhancements
Implement asynchronous/background job processing
Add real-time loading or progress indicators
Implement polling mechanism for job status updates
Add proper success callback responses
Implement timeout warning and recovery handling
