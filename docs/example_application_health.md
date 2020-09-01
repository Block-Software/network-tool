---
layout: page
title: Example health state change - Application
permalink: /example_data/health
---

## Application health state change - workflow

When a callback from a monitoring item (Example Data)[/example_data] fails its callback, the following trigger steps are carried out:

### Application issue detected on local device  / agent

* Report back to central server and query reported application health:
  * Devices affected on the same switch / stack
    * Other devices affected in this specific area would indicate a problem with the network:
      * Network ticket raised if one not already raised
  * Globally for all users
    * Many users affected means it's likely an application issue:
      * Raise a service down ticket for the application if one not already raised
  * No other devices affected
    * This indicates a user / device specific problem:
      * Initiate troubleshooting steps for wired / wireless connectivity

### Troubleshooting - Wireless

* Ping default gateway
* Ping local DNS server
* Collect endpoint enrichment details from centralised network database based on users MAC address:
  * GET - https://<IP>/dna/intent/api/v1/issue-enrichment-details 
* Run check against the RSSI dBm to check historical (last 15 minutes and current) threshold
  * `netsh wlan show interface`
    * Calculated as `$percentage/2-100`
* Run a check against the SNR dB to check historical (last 15 minutes and current)
* Check receive rate and transmit rate (last 15 minutes and current)
  * `netsh wlan show interface`
* Check what channel the client adapter is on
* Check Wireless adapter settings to make sure they are consistent with location guidelines
  * if not, resolve:
    * Power saving
    * Disabled channels
    * MIMO configuration
    * Roaming aggressive configuration
    * Transmit power
* Check Wireless adapter make/model to make sure they are running an approved driver version

### Troubleshooting - Wired

* Ping default gateway
* Ping local DNS server
* Collect endpoint enrichment details from centralised network database based on users MAC address:
  * GET - https://<IP>/dna/intent/api/v1/issue-enrichment-details 
* Initiate a call from the server to the local network switch based on known location endpoint data to analyse:
  * Input errors
  * Output errors
  * Collisions
  * Negotiated speed
  * Negotiated Duplex
  * Check for cable diagnostics
* Check Wired network adapters settings to make sure they are consistent with location guidelines
* Check Wired adapter make/model to make sure they are running an approved driver version
