# Azure-Sentinel-Honeypot
Setup Azure Sentinel and a honeypot to observe live RDP brute force attacks and plot attacker geolocation

![image](https://github.com/ahmed86-star/Azure-Sentinel-Honeypot/assets/113064932/1564a35b-a4a4-4858-a21d-c17dac3c2864)


# Azure Sentinel Honeypot

This project demonstrates how to set up Azure Sentinel to monitor a honeypot virtual machine for live RDP brute force attacks, retrieve the attackers' geolocation information using a custom PowerShell script, and plot the data on the Azure Sentinel Map.

This project sets up a honeypot to attract and log RDP brute force attacks, uses Azure Sentinel for monitoring, and plots the attackers' geolocation on a map.

## Prerequisites

- Azure subscription free https://azure.microsoft.com/en-us/free
- Basic understanding of Azure Sentinel and PowerShell
- Virtual Machine to act as a honeypot (windows 10)

## Setup Guide

### Setting up the Honeypot VM

1. Create a new Virtual Machine in Azure.
2. Configure the VM to allow RDP connections.
3. Harden the VM with minimal security to act as a honeypot.

### Configuring Azure Sentinel

1. Set up Azure Sentinel in your Azure portal.
2. Connect the honeypot VM to Azure Sentinel.
   - Go to Azure Sentinel -> Data connectors.
   - Add a new connector for your VM.

### Creating the PowerShell Script

1. Create a PowerShell script to fetch geolocation data for IP addresses.
2. Sample PowerShell script:
# Import the necessary module
Import-Module Az

# Define the function to get geolocation data using the IP Geolocation API
function Get-Geolocation {
    param (
        [string]$ip
    )
    
    $apiKey = "8a19764ae73944c4bca21b98261d9aa9"
    $url = "https://api.ipgeolocation.io/ipgeo?apiKey=$apiKey&ip=$ip"
    $response = Invoke-RestMethod -Uri $url -Method Get
    return $response
}

# Retrieve RDP login failure events
$rdpLogs = Get-WinEvent -LogName 'Security' | Where-Object { $_.Id -eq 4625 }

# Loop through each log entry and get geolocation data
foreach ($log in $rdpLogs) {
    $ip = $log.Properties[18].Value
    $location = Get-Geolocation -ip $ip
    Write-Output "IP: $ip, Location: $($location.city), $($location.country_name)"
}



3. Deploy the script to your honeypot VM.

## Observing Attacks

1. Use Azure Sentinel to monitor and log the RDP brute force attempts on the honeypot VM.
2. Analyze the logs to identify attack patterns and source IPs.  
![image](https://github.com/ahmed86-star/Azure-Sentinel-Honeypot/assets/113064932/669e90d7-564a-4c34-9581-3ca3fbe97b63)
## Plotting Geolocation Data

1. Integrate the PowerShell script with Azure Sentinel to fetch and plot geolocation data.
2. Use Sentinel’s map feature to visualize the attacker locations.

## Conclusion

This project demonstrates how to set up a honeypot and use Azure Sentinel for monitoring and analyzing RDP brute force attacks, including plotting the attackers' geolocation data on a map.
