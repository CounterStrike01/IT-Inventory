# IT-Inventory
This script generates an interactive HTML dashboard from Microsoft Intune (via Microsoft Graph) to help IT admins quickly inventory managed devices, visualize compliance/security posture, and export shareable reports for ops and leadership. It connects using app‑only credentials, pulls device data with the Microsoft Graph PowerShell SDK


How it works (high‑level flow)


Auth (client credentials)
The script posts to Azure AD’s token endpoint to obtain an app‑only access token and then calls Connect-MgGraph -AccessToken $SecureToken -NoWelcome. Microsoft Graph PowerShell supports delegated and app‑only flows; with app‑only, permission is granted through the app registration. [learn.microsoft.com]


Query Intune
Using Get-MgDeviceManagementManagedDevice -All -Property "<comma-separated>$select", the script retrieves all pages of managed devices and only selected fields. Filtering and $select reduce payload size and improve performance. [learn.microsoft.com], [petervanderwoude.nl]


Shape the inventory
PowerShell Where-Object, Group-Object, and Select-Object create arrays for:

OS families, top manufacturers, top Windows OS versions
Compliant vs noncompliant
Protected vs unprotected (Defender real-time protection)
Encrypted vs unencrypted (BitLocker)
Azure registered vs unregistered
Company vs personal ownership
Inactive (≥30 days) and low storage (< 100 GB)

(These are all derived from standard managedDevice fields.) [learn.microsoft.com]


Render HTML
Inside a single New-HTML { ... } block, PSWriteHTML builds sections, panels, charts, and DataTables. This module abstracts HTML/CSS/JS behind PowerShell functions so you can author reports without web dev work.

