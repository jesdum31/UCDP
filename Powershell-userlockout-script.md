# PowerShell Script: Automatic User Lockout Based on Failed Login Attempts

This script checks Windows Event Logs for failed login attempts (Event ID 4625).  
If a user has failed to log in more than the threshold within a 15-minute window, their account is locked.  
The script is intended to run every 30 minutes via Task Scheduler.

```powershell
# Set the threshold for failed attempts
$Threshold = 3  
$TimeFrame = (Get-Date).AddMinutes(-15)  # Look back 15 minutes for failed logins

# Get failed login attempts from Event Viewer (Event ID 4625)
$FailedLogins = Get-WinEvent -FilterHashtable @{
    LogName = "Security"
    ID = 4625
    StartTime = $TimeFrame
} | ForEach-Object {
    # Extract the username from the event message
    $User = ($_ | Select-Object -ExpandProperty Properties)[5].Value
    if ($User -match "^\S+$") { $User }  # Exclude empty or invalid entries
} | Group-Object -NoElement

# Process each user who exceeded the failed login threshold
foreach ($User in $FailedLogins) {
    if ($User.Count -ge $Threshold) {
        Write-Output "Locking user: $($User.Name) due to $($User.Count) failed login attempts."

        # Lock (disable) the user account
        Start-Process -NoNewWindow -FilePath "net" -ArgumentList "user $($User.Name) /active:no"

        Write-Output "User $($User.Name) has been locked out."

        # Log the lockout action
        Add-Content -Path "C:\lockout_log.txt" -Value "$(Get-Date) - Locked out $($User.Name) due to $($User.Count) failed login attempts."
    }
}

# Unlock users every 30 minutes (to be scheduled)
Get-LocalUser | Where-Object { $_.Enabled -eq $false } | ForEach-Object {
    Start-Process -NoNewWindow -FilePath "net" -ArgumentList "user $($_.Name) /active:yes"
    Add-Content -Path "C:\lockout_log.txt" -Value "$(Get-Date) - Unlocked user $($_.Name)"
}
