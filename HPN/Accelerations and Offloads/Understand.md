# Understand: Accelerations and Offloads

Processing of received packets can be done in software by the host CPUs or offloaded to the NIC and passed directly into memory. The path a packet traverses prior to reaching its ultimate destination (application) is known as its datapath. Accelerations and Offloads can be divided by their native, synthetic, or hardware datapaths.

The chosen datapath is per network adapter and flow. A single host can leverage all three datapaths simultaneously while a single adapter can leverage different datapaths per data flow. For example, a host may have three NICs, each passing network traffic through a different data path. Alternatively, a host could have a single NIC that uses different datapaths for different traffic types or applications; the options and ultimate choice depends on your goals and scenarios.

The performance, flexibility, configuration complexity, and security of each datapath can be summarized as follows:

<table>
<thead>
<tr class="header">
<th><strong>Datapath</strong></th>
<th><strong>Features</strong></th>
<th><strong>Performance</strong></th>
<th><strong>Configuration Complexity</strong></th>
<th><strong>Security</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Native</td>
<td>High</td>
<td>Medium</td>
<td>Low</td>
<td>High</td>
</tr>
<tr class="even">
<td>Synthetic</td>
<td>Medium</td>
<td>Low</td>
<td>Medium</td>
<td>High</td>
</tr>
<tr class="odd">
<td>Hardware</td>
<td>Low</td>
<td>High</td>
<td>Medium (Native)<br />
Highest (Virtualized)</td>
<td>Medium (Native)<br />
Low (Virtualized)</td>
</tr>
</tbody>
</table>


## Data Paths

This section describes the different data paths that network traffic can take on a Windows host and the different categories of accelerations and offloads.

### Native Datapath

#### Description
The native datapath describes packets received on any adapter that does not have direct access to the hardware (NIC) and is not attached to a virtual switch. This is the default datapath after Windows is installed.

The picture below shows data travelling on the native datapath; coming off the wire, entering the physical port of the physical adapter, then through the miniport driver and ultimately operating system.

![Native Data Path](https://github.com/microsoft/HPN/blob/master/HPN/Accelerations%20and%20Offloads/Media/Native%20Data%20Path.png)

#### When you would use this Datapath

Any adapter that is not attached to a Hyper-V Virtual Switch, not using RDMA, or other Direct Memory Access technology such as SR-IOV.

#### Benefits

  - Easy to configure (default configuration for Windows)

  - No overhead from the Hyper-V virtual Switch

  - Medium performance

#### Drawbacks

  - No virtualization scenarios

  - Packet processing consumes Host CPU cycles

#### Scenarios

Default out of the box Windows networking. Non-Virtualized Scenarios that do not use RDMA or SR-IOV to pass data directly into memory. Common examples include:

  - Web Front-End Servers (Baremetal)

  - Remote Desktop Services – Session based virtualization

  - Systems that demand lower latency over the availability, maintenance, and consolidation benefits of virtualized infrastructure

### Synthetic Datapath

#### Description
The synthetic datapath describes packets received on any adapter that is bound to a virtual switch that is not optimized with direct access to hardware.  This is the default datapath for adapters attached to the Hyper-V Virtual Switch.  This includes virtual NICs (vNIC) in the host OS as well as virtual machine NICs (vmNIC) in guest virtual machines and containers.

The picture below shows data travelling on the synthetic datapath; coming off the wire, entering the physical port of the physical adapter, then through the miniport driver.  Next it travels through the virtual switch before riding along the VMBus and ultimately reaching a vNIC or vmNIC.

![Synthetic Data Path](https://github.com/microsoft/HPN/blob/master/HPN/Accelerations%20and%20Offloads/Media/Synthetic%20Data%20Path.png)

#### When you would use this Datapath

Data travelling to any virtual NIC that is attached to a Hyper-V Virtual Switch and is not optimized with direct access to hardware.

#### Benefits

  - Generally simple to configure

  - Full benefits of the Software Defined Datacenter

  - Provides virtualization support

#### Drawbacks

  - Small performance loss from the Hyper-V virtual Switch

  - Packet processing consumes Host CPU cycles

#### Scenarios

Common examples include (but are not limited to):

  - Hyper-V Compute (Host and Guest Virtual NICs)

  - Windows and Hyper-V Containers

  - Storage Spaces Direct vNICs that do not use RDMA

  - Software Defined Networking

### Hardware Datapath

The hardware datapath describes packets received on any adapter that is optimized with Direct Memory Access (DMA) technology (for example SR-IOV). The virtual switch may or may not be attached to this adapter, however packets travelling on this datapath will not traverse the virtual switch to reach their destination.

The picture below shows data travelling on the hardware datapath; coming
off the wire, entering the physical port of the physical adapter. Next,
a physical or virtual function is engaged prior to reaching the
corresponding miniport in the host or virtual machines.

![Hardware Data Path](https://github.com/microsoft/HPN/blob/master/HPN/Accelerations%20and%20Offloads/Media/Hardware%20Data%20Path.png)

#### When you would use this Datapath

Any adapter that uses a Direct Memory Access (DMA) technology such as RDMA (Remote Direct Memory Access) including Guest RDMA, or SR-IOV (Single Root IO-Virtualization). Packets on this datapath have a direct link that reduces latency and bypasses the OS software stack.

#### Benefits

  - Low host CPU processing
  - Low latency
  - Increased throughput
  - Support for native and virtualized environments

#### Drawbacks

  - The guest cannot participate in Microsoft SDN

  - No support for multicast or broadcast traffic

  - Reduced security boundary
      - No modern QoS support
      - Guests operate in the default IEEE 802.1p class of service

#### Scenarios

Some Hyper-V Virtualized Scenarios. Common examples include:

  - Hyper-V Compute
    
      - Host SR-IOV Physical Function
    
      - Guest SR-IOV Virtual Function

      - Guest RDMA

  - Remote Direct Memory Access (RDMA) including:

      - Storage Spaces Direct adapters

      - Other NICs using SMB

## Acceleration Categories

Accelerations can be implemented in one or more locations…