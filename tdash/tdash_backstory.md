# Tdash backstory

For a number of years I had been looking for a detailed thread network dashboard and cli tools to help me better understand and improve my thread network: multiple TBRs, a couple dozen thread devices (both Apple Homekit over Thread and Matter over Thread). 

I use these thread tools: OTBR ot-ctl cli, matterjs-server thread dashboard (great tool and nice direction of the new features), Eve app (good thread device view) and the Open Thread Border Router (OTBR) console log and topology view.

In addition to Matter over Thread, I wanted Apple Homekit (HAP) over thread support for my thread network.

I also wanted to drill into the next level of detail: filter by MAC (radio) and MLE (mesh) counters, Link Quality; decode all the thread related mDNS records; access to the thread device data in a local store to enable building diagnostics tools. 

I built tdash dashboard and tools which has helped me better understand and improve my thread network: add more thread routers in the right areas. 

Links to related Thread Network tools: [Home Assistant Matter Server](https://github.com/matter-js/matterjs-server) has a great dashboard for Matter over Thread devices; [Eve App](https://www.evehome.com/en-us/eve-app) uses a powered Eve device (smartplug) in the thread network to enable gathering thread device information; [Thread Group - Android: Thread Network Diagnostics app](https://play.google.com/store/apps/details?id=com.threadgroup.otloom&hl=en_US); [Nordic Semiconductor - nRF Thread Topology Monitor](https://www.nordicsemi.com/Products/Development-tools/nRF-Thread-topology-monitor).


## Thread Environment

### Thread Border Routers

- Apple TVs & HomePod Minis
- Openthread OTBR in a Docker container connected to a Home Assistant Connect ZBT-1 via usb. Proxmox VM on MiniPC: 
- HA OTBR App running in HAOS connected to a Home Assistant Connect ZBT-2 via usb. Proxmox VM on MiniPC: 

### Tdash dashboard and tools 

- Running inside a Docker container on Debian. Also run directly on the Debian Linux host. Proxmox VM on MiniPC: 

### Mix of thread devices 

- Apple HomeKit HAP & Matter devices
- Eve, Aqara, Ikea, Schlage, Govee, Nanoleaf, Third Reality; across a number of device types: smart outlets, climate sensors (temperature, humidity), Room sensors (motion, occupancy, presence), contact sensors, smart water valves, water leak sensors, buttons, etc
