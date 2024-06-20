# Azure-Sentinel-Honeypot
Setup Azure Sentinel and a honeypot to observe live RDP brute force attacks and plot attacker geolocation



![Failed RDP World Map](![image](https://github.com/ahmed86-star/Azure-Sentinel-Honeypot/assets/113064932/41a18683-d973-4cbe-9b25-4d50b39ef276)

## Introduction

This project sets up a honeypot to attract and log RDP brute force attacks, uses Azure Sentinel for monitoring, and plots the attackers' geolocation on a map.

## Prerequisites

- Azure subscription https://azure.microsoft.com/en-us/free
- Basic understanding of Azure Sentinel and PowerShell
- Virtual Machine to act as a honeypot

## Setup Guide

### Setting up the Honeypot VM

1. Create a new Virtual Machine in Azure.
2. Configure the VM to allow RDP connections.
3. Harden the VM with minimal security to act as a honeypot. (windows 10)

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

![image](https://github.com/ahmed86-star/Azure-Sentinel-Honeypot/assets/113064932/07475778-33e3-4525-b380-ff2229a88903)

![image](https://github.com/ahmed86-star/Azure-Sentinel-Honeypot/assets/113064932/062127a9-42eb-4f0d-ab51-defecf60acb3)

# Loop through each log entry and get geolocation data
foreach ($log in $rdpLogs) {
    $ip = $log.Properties[18].Value
    $location = Get-Geolocation -ip $ip
    Write-Output "IP: $ip, Location: $($location.city), $($location.country_name)"
}

