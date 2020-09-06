# Veeam Endpoint Database Reset

If you ever need to restart a veeam endpoint backup:

* Delete or move the current backup folder
* Stop the veeam services.
* Regedit:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Veeam\Veeam Endpoint Backup`
    * DWORD: Recreatedatabase
    * Value: 1

Start the Veeam services, this can take 5+ minutes.
