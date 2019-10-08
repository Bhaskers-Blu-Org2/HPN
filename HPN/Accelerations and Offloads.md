# Accelerations and Offloads

> Applies to: Windows Server 2016, Windows Server 2019

In this topic, we give an overview of the different network acceleration and/or offload technologies available in Windows Server 2016 and Windows Server 2019 and discuss how they help make networking more performant and/or efficient.

Network accelerations and offloads allow your system to better utilize system resources while reaching the maximum possible throughput and lowest latency. For example, without network offloads, all network traffic would be processed by a single CPU core; the total network throughput for a single system would be limited to the processing capability of a single CPU core. A single CPU core typically can process anywhere between 3 and 5 Gbps – With modern NICs reaching 10, 25, 50, 100, and 200 Gbps, this simply won’t suffice.

To overcome the single CPU core limitation, one or more CPU spreading acceleration technologies can be implemented.

This section will help you:

  - [[Understand](Accelerations and Offloads/Understand.md)
    
      - How they work

      - Different data paths and when they are used

  - [Deploy](Accelerations and Offloads/Deploy.md)

  - [Validate](Accelerations and Offloads/Validate.md)
