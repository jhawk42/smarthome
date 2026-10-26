# Thread Note - Signal RSSI and Margin

Source: Google Gemini 

Tip: Battery-powered Thread
accessories (like door sensors) do not repeat signals. If you see a poor RSSI or margin on a sensor, adding a plug-in, mains-powered Thread device (like a smart plug) halfway between the sensor and the rest of the mesh

## Key Concepts
* RSSI (Received Signal StrengthIndicator):
* The total power level received by an antenna, measured in negative decibel-milliwatts (dBm).
* Values closer to 0 represent a stronger signal (e.g., -50 dBm is great, -85 dBm is weak). 
	
* Margin (Link/Fade Margin):
* The buffer difference between your actual received signal (RSSI) and the receiver's sensitivity threshold or noise floor.

## Thread RSSI Ranges (Signal Strength)
When viewing diagnostics in smart home platforms like Home Assistant, Apple Home, or proprietary developer consoles, use these benchmarks to judge the signal health between any two nodes:
* -30 to -50 dBm | Excellent: Nodes are likely in the same room or very close with a direct line of sight. 
* -55 to -70 dBm | Great / Stable: The optimal zone for a responsive mesh network. Low latency and minimal to no packet loss.
* -71 to -85 dBm | OK / Acceptable: A functional connection, but hitting the upper boundary for latency-critical devices like smart lighting. It is heavily reliant on the mesh network to route around obstacles if the signal dips any lower. 
* -86 to -94 dBm | Poor: Highly unstable. You will experience delayed device responses, temporary "No Response" errors, and occasional packet drops.
* -95 dBm or lower | Critical: The edge of the radio's capability. Total connection loss is frequent.

## Thread Link Margin Ranges
Because Thread chips are built to tolerate weak signals, their Link Margin (the buffer between the current RSSI and the absolute failure point) has a slightly lower acceptable threshold than standard consumer tech: 

* > 25 dB | Excellent: Total immunity to temporary interference.
* 15 dB to 25 dB | Good: Fully reliable for typical home deployments.
* 10 dB to 14 dB | Fair: Vulnerable if a physical obstacle changes (e.g., someone closes a heavy wood door or stands between the devices).
* < 10 dB | Poor: Too narrow a butter.

The link will likely fail when 2.4 GHz WiFi interference peaks.