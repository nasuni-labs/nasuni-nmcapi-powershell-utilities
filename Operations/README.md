# nmc-api-powershell-utilities
Utlilities and scripts that use the NMC API to perform operations and generate reports

# Operations
PowerShell NMC API Scripts to assist with daily Nasuni operations.

## Delete Sync Errors
While the NMC UI does not expose a way to bulk delete/acknowledge sync errors, customers can use the NMC API Messages endpoint to list and delete sync errors. This script deletes sync errors by using the Messages NMC API endpoints to list and delete messages that match the specified status codes and type.\
**NMC API Endpoints Used**: list messages - http://docs.api.nasuni.com/nmc/api/1.1.0/index.html#nasuni-management-console-api-messages; delete message - http://docs.api.nasuni.com/nmc/api/1.1.0/index.html#delete-message \
**Required Inputs**: NMC hostname, username, password, StatusCode, StatusType, limit\
**Status codes**: set GFL for path (fsbrowser_globallock_edit); Refresh info for path (fsbrowser_stat_item); Create a Share (volumes_shares_add)\
**Compatibility**: Nasuni 8.0 or higher required\
**Name**: DeleteSyncErrors.ps1

## Export NMC Messages to CSV
Customers have requested that we supply robust auditing for actions performed using the NMC API or GUI (PM-320). While we don’t currently audit all NMC actions, the NMC API Messages endpoint currently logs activity performed by NMC GUI and NMC API, including the action performed and the user that initiated it . This script lists all messages that are currently available in the NMC API messages list, sorts them by send_time, and exports them to timestamped CSV.

Note: NMC Messages will only show recent activity since a cron runs on the NMC every 20 minutes that removes messages that are transient and 20 minutes old. In order to capture a full picture of NMC events for logging, run this script every 5 minutes using a cron or Windows Scheduled Task. The exported CSVs of NMC messages can be concatenated and sorted to show all of the NMC activity on a daily basis using the ConcatenateNMCMessages.ps1 script.\
**NMC API Endpoints Used**:list messages - http://docs.api.nasuni.com/nmc/api/1.1.0/index.html#nasuni-management-console-api-messages \
**Required Inputs**: NMC hostname, username, password, ReportFile (where to save the CSV), limit (number of messages to return).\
**Compatibility**: Nasuni 8.0 or higher required\
**Name**: ExportMessagesToCSV.ps1

## Export Health Monitor Status for All Edge Appliances
Uses PowerShell to export a list of Health Monitor status for Edge Appliances and export the results to a CSV.\
**NMC API Endpoints Used**: List health status for all Edge Appliances - http://docs.api.nasuni.com/nmc/api/1.1.0/index.html#list-health-status-for-all-filers \
**Required Inputs**: NMC hostname, username, password, reportFile, limit\
**Compatibility**: Nasuni 8.8 or higher required\
**Output CSV content**: filer_serial_number, filer_name,last_updated,network,filesystem,cpu,nfs,memory,services,directoryservices,disk,smb\
**Name**: ExportHealthToCSV.ps1

## Export Edge Appliance Status to CSV
The NMC List Edge Appliances endpoint provides a list of all Edge Appliances, their status, and settings configured for each. This script lists all Edge Appliances in an account along with their status and exports them to CSV. The script does not include the enumeration and export of Edge Appliance settings, but that could easily be added in a future version. \
**NMC API Endpoints Used**: List Edge Appliances - http://docs.api.nasuni.com/nmc/api/1.1.0/index.html#list-filers \
**Required Inputs**: NMC hostname, username, password, ReportFile (where to save the CSV), limit (number of Edge Appliances to return).\
Export Contents: Description, SerialNumber, GUID, build, cpuCores, cpuModel, cpuFrequency, cpuSockets, Memory, ManagementState, Offline, OsVersion, Uptime, UpdatesAvailable, CurrentVersion, NewVersion, PlatformName, cacheSize, cacheUsed, cacheDirty, cacheFree, cachePercentUsed\
**Compatibility**: Nasuni 7.10 or higher required\
**Name**: ExportEAStatusToCSV.ps1

## List Cloud Credentials
Lists cloud credentials for an account and exports results to the PowerShell console. \
**NMC API Endpoint Used**: list cloud credentials - http://docs.api.nasuni.com/nmc/api/1.1.0/index.html#list-cloud-credentials \
**Required Inputs**: NMC hostname, username, password\
**Output**: cred_id, name, filer_serial_number, cloud_provider, account, hostname, status, note, in_use\
**Compatibility**: Nasuni 8.0 or higher required\
**Name**: ListCloudCredentials.ps1

## Get Message
This script gives you an example using the message ID to look up the status of an action. The NMC is an asynchronous API and POST or UPDATE actions you initiate with the NMC API will return a “pending” status along with an ID that you can then check to see the status of the request once it has been processed. The screenshot below is the result of a POST request to the NMC API. The red box is the message ID you will use for the messageID in the script. The green box gives you the full URL to the messages NMC API endpoint including the ID.\
**NMC API Endpoint Used**: Get Message - http://docs.api.nasuni.com/nmc/api/1.1.0/index.html#nasuni-management-console-api-messages \
**Required Inputs**: NMC hostname, username, messageID\
**Output**: Example below is of a message for an action that failed. A successful message will show “synced” as the status.\
**Compatibility**: Nasuni 7.10 or higher required\
**Name**: GetMessage.ps1

## Export Edge Appliance Volume Settings to CSV
This script exports all Edge Appliance settings that are applied on a per-Volume/per-Edge Appliance basis to CSV. The output of these scripts can be used as a reference for updating or validating settings when detaching and re-attaching volumes during cloud to cloud migration. The script exports the following settings and logs them to the listed file name:

| Setting | File Name | Description | CSV Columns |
| ------- | --------- | ----------- | ----------- |
| CIFS Shares | CifsShares.csv | All CIFS shares listed in the NMC | shareid, Volume_GUID, filer_serial_number, share_name, path, comment, readonly, browseable, authAuthall, authRo_users, authRw_users, authDeny_users, authRo_groups, authRw_groups, authDeny_groups, hosts_allow, hide_unreadable, enable_previous_vers, case_sensitive, enable_snapshot_dirs, homedir_support, mobile, browser_access, aio_enabled, veto_files, fruit_enabled, smb_encrypt, shared_links_enabled, link_force_password, link_allow_rw, external_share_url, link_expire_limit, link_authAuthall, link_authAllow_groups_ro, link_authAllow_groups_rw, link_authDeny_groups, link_authAllow_users_ro, link_authAllow_users_rw, link_authDeny_users |
| NFS Exports | NfsExports.csv | All NFS exports listed in the NMC | exportId, Volume_GUID, filer_serial_number, export_name, path, comment, readonly, allowed_hosts, access_mode, perf_mode, sec_options, nfs_host_options |
| FTP Directories | FtpDirectories.csv | All FTP shares listed in the NMC | FtpId, Volume_GUID, filer_serial_number, ftp_name, path, comment, readonly, visibility, ip_restrictions, allowed_users, allowed_groups, allow_anonymous, anonymous_only, Permissions_on_new_files, hide_ownership, use_temporary_files_during_upload |
| Snapshot and Sync Schedule | SnapAndSyncSchedule.csv | List of the configured snapshot and sync schedule for all volumes and Edge Appliances | VolumeName, FilerName, VolumeGuid, FilerSerialNumber, SnapSchedMon, SnapSchedTue, SnapSchedWed, SnapSchedThu, SnapSchedFri, SnapSchedSat, SnapSchedSun, SnapSchedAllday, SnapSchedStart, SnapSchedStop, SnapSchedFrequency, SyncSchedMon, SyncSchedTue, SyncSchedWed, SyncSchedThu, SyncSchedFri, SyncSchedSat, SyncSchedSun, SyncSchedAllday, SyncSchedStart, SyncSchedStop, SyncSchedFrequency, SyncSchedAutocacheAllowed, SyncSchedAutocacheMinFileSize |
| Volume Auditing | VolumeAuditing.csv | List of volume auditing settings for all volumes and Edge Appliances | VolumeName, FilerName, VolumeGuid, FilerSerialNumber, AuditingEnabled, Create, Delete, Rename, Close, Security, Metadata, Write, Read, PruneAuditLogs, DaysToKeep, ExcludeByDefault, IncludeTakesPriority, IncludePatterns, ExcludePatterns, SyslogExportEnabled |
| Folder Quotas and Rules | FolderQuotas.csv | List of all folder quotas and rules for all volumes and Edge Appliances | Quota ID, VolumeGuid, FilerSerial, Path, Quota Type, Quota Limit, Quota Usage, Email |
| Auto Cache Folders | AutoCacheFolders.csv | List of auto cache folders for all volumes and Edge Appliances | volume_guid, filer_serial_number, path, auto cache mode |
| Pinned Folders | PinnedFolders.csv | List of pinned folders for all volumes and Edge Appliances | volume_guid, filer_serial_number, path, pinning mode |
| File Alert Service | FileAlertService.csv | List of file alert service entries for all volumes and Edge Appliances | VolumeName, FilerName, Volume GUID, FilerSerialNumber, File Alerts Enabled, File Alert Patterns |
| Snapshot Directory Access | SnapshotDirAccess.csv | List of the snapshot directory access configuration for all volumes and Edge Appliances | VolumeName, FilerName, Volume GUID, FilerSerialNumber, Snapshot Access Enabled |

**Required Inputs**: NMC hostname, username, password, reportDirectory (where to save the CSV files), limit (limit to use for each API endpoint).\
**Compatibility**: Nasuni 7.10 or higher required\
**Name**: ExportEaVolumeSettings.ps1

## Export NMC Notifications to CSV
Exports NMC Notifications to CSV.\
**NMC API Endpoints Used**: ist notifications - http://docs.api.nasuni.com/nmc/api/1.1.0/index.html#nasuni-management-console-api-notifications \
**Required Inputs**: NMC hostname, username, password, ReportFileName, limit (number of notifications to return)\
**Compatibility**: Nasuni 8.0 or higher required\
**Name**: ExportNotificationsToCSV.ps1
