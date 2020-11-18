### Differences between Containers and Virtual Machines (VMs)

Containers and Virtual Machines are both used to create **isolated** virtual environments which can be used to develop and test applications.


|  **VM** 	|  **Container** 	|
|---	|---	|
|  **VM** is an operating system that shares the physical resources of one server.	|   **Container** isolates the app from the OS and runs multiple workloads on a single OS instance.	|
|   **VMs** have their own infrastructure and are self-contained. |   **Container** shares the resources of a single OS to run the different functions of an app.  |
|  A **VM** requires its own OS which can be different from the host OS. |  The **Container** shares the bins / libraries and runtime of the underlying OS. 	|
|  A **VM** virtualizes the hardware. |  **Container** virtualizes the OS. 	|
|  A **VM** is considered to be less portable due to the OS restriction. |  **Container** more easily portable. |
|  A **VM** slower to boot. |  **Container** more quicker to boot. 	|
|  **VM** size is in tens of GBs |  **Container** size is in tens of MBs. 	|

---


Ref: [Containers vs VMs](https://phoenixnap.com/kb/containers-vs-vms)

