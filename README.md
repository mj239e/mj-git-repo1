MechID and Certificate Rotation Process
Overview
This document provides an overview of the MechID and Certificate Rotation process, including scheduling, change management (CM), pre-checks, execution, and post-rotation validation.

Scheduling
We maintain a centralized calendar for all scheduling activities. The calendar can be accessed via the following wiki link:
Scheduling Calendar

Site Scheduling
Scheduling depends on whether the site falls under Network Cloud (NC) or AIC.

Network Cloud (NC) Sites:

Use the 3C portal to check for upcoming MechID and SSL/TLS certificate expirations.
3C Portal
Use the Deep Portal to identify upcoming expiring sites/zones.
Monitor email notifications and tracking tools for certificate and MechID expiration alerts.
Utilize "near-expiring" reports from the deployment team for further confirmation.
AIC Sites:

Expirations are tracked primarily through scripts.
Cross-check data with the Lab Operations | Certificates Rotation section in the Deep Portal.
Change Management (CM) Process
Once the expiring sites/zones are identified:

Open a JSD Change Management (CM) request and await approval.
JSD CM Request
Once CM is approved, submit a Tower request to suppress JSD alarms for the specific NC site/lab.
Tower Request
Pre-Checks for Site Rotations
Pre-checks vary based on the type of site:

Network Cloud (NC) Pre-Checks
Ensure the site is loaded with the SwissKnife MechID.
Verify that the health check report passes all checks within Jenkins.
If issues arise, troubleshoot common errors.
If unresolved, escalate in the SSL and MechID Rotation MS Teams chat.
MS Teams Chat
Ensure the Tower request is submitted at least 24 hours before the rotation.
Tower Request Process
Confirm that the CM request is fully approved and does not have conflicting schedules.
AIC Pre-Checks
AIC sites require on-the-day preparation instead of advance health checks.
Day of Rotation Execution
Network Cloud (NC) Execution
Follow the NC MechID & Certificate Rotation MOP.
NC Rotation MOP
Submit patches or patch sets (PS) using the latest Jenkins CH (WNV2c).
Jenkins CH (WNV2c)
Once patches are reviewed and merged, begin CM implementation.
Pipeline Execution:
Use CH auk60z for 2.11 & above sites.
Use rdm59z for 2.10 & below sites.
Execute the two main pipelines:
Certificate Rotation Pipeline
Site Update Pipeline
Once pipelines complete successfully, perform post-checks and mark the change window as resolved.
Additional Step for NC Sites with Pure Storage
If the site has Pure Storage, update the PURE API Token as part of the rotation.
The Storage team generates a new token only when the change/disruptive window is active.
Add the new token into the patch set (add-secret-document) and execute the pipelines as usual.
Post-Rotation Validation
Certificate Validation:

Ensure internal certificates are rotated and expiration dates reflect a 12-month validity.
Validate via KeyFactor, which integrates with DigiCert.
KeyFactor Tool
Monitoring & Health Checks:

Check Nagios dashboard for new failures (note: some known failures may exist).
Verify Nagios and Horizon dashboards for access issues.
Confirm the expiration dates of the certificates within a browser.
Alias Check:

Run a Bash script to ping each alias within the certificate to confirm all aliases reflect the renewed dates.
KeyFactor Cleanup:

Ensure older certificates are removed from the KeyFactor inventory.
Confirm that no expiration alerts are being escalated for retired certificates.
This document serves as a reference for all MechID and Certificate Rotation activities. Please ensure compliance with these steps for successful rotations.
