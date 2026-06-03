# High-Performance Windows Remote Desktop System (C++)

This project is an **ultra-low-latency, high-performance Windows remote control system** developed in C++. Adopting a classic Client/Server (C/S) architecture, the system is deeply optimized for real-time video streaming. By leveraging a dual-channel network architecture (TCP signaling / UDP video) and an end-to-end hardware acceleration pipeline, it delivers a seamless **1080P 60FPS** remote control experience across both LAN and WAN/NAT-traversal environments.

---

## 🚀 Core Architecture & Technical Highlights

Completely discarding traditional GDI screen scraping and CPU encoding methods, this system establishes an **end-to-end hardware-accelerated pipeline covering capture, encoding, transmission, decoding, and rendering**:

### 1. Hardware-Accelerated Pipeline
* **Low-Level Screen Capture:** Delves into the Windows kernel to implement high-performance screen capture utilizing the **DXGI (Desktop Duplication API)**, perfectly supporting high-framerate capture at **1080P 60FPS**. The codebase pre-allocates and encapsulates the `ID3D11Texture2D` direct capture interface, offering excellent extensibility for GPU interaction.
* **GPU Hardware Encoding:** Integrates the NVIDIA hardware acceleration interface, **NVENC**, leveraging the GPU’s independent multimedia engine for real-time H.264 encoding. It enables the **`zerolatency` preset**, drastically reducing encoding latency and CPU overhead.
* **GPU Hardware Decoding & Rendering:** The client side utilizes **GPU hardware decoding** and renders images directly via **Direct3D (D3D)**. By eliminating CPU software decoding and memory copies (zero-copy philosophy), it achieves seamless, high-efficiency frame output.

### 2. Dual-Channel Network Architecture & Custom Protocol Design
* **Signaling/Video Separation:** Designed with a dual-communication channel architecture. Mouse/keyboard control commands and heartbeat packets are transmitted over a **TCP channel** to guarantee precision and zero packet loss. Concurrently, video bitstreams flow over a **UDP channel** to ensure maximum real-time performance.
* **Custom Application-Layer Protocol:** Features a self-developed proprietary streaming header. The packet format includes fields for `Length`, `Frame ID`, and `Slice Index`, enabling the dynamic fragmentation and reassembly of large video frames.
* **Adaptive Packet-Loss Strategy (Anti-Jitter):** Tailored for UDP weak-network conditions, this strategy incorporates an active-discard policy. When packet loss is detected for the current frame, or when a newer Frame ID is received, the system proactively drops stale historical fragments and transitions smoothly to the latest frame, preventing frame accumulation and latency spikes.
* **Multi-Threaded Concurrency Control:** The client employs a highly efficient **dual-thread asynchronous architecture**, fully decoupling "network data reception" from "video stream hardware decoding" to eliminate blocking and maximize throughput.

### 3. Network Topology & Penetration Capabilities
* Natively supports high-bandwidth transmission over Local Area Networks (LAN).
* Supports deployment through public cloud servers (e.g., Alibaba Cloud) utilizing an **FRP (Fast Reverse Proxy)** architecture, enabling secure **NAT traversal** and seamless cross-regional remote control capabilities.

---

## 🛠️ Tech Stack

* **Programming Language:** Modern C++ (C++11/14)
* **Multimedia & Graphics:** FFmpeg, NVENC (NVIDIA GPU Encoding), DXGI API, Direct3D (Client Hardware Rendering)
* **Network Programming:** High-performance WinSock programming, Dual TCP/UDP protocols, Custom application-layer streaming protocol
* **NAT Traversal:** FRP, Alibaba Cloud (ECS)

---

## 📦 Getting Started

### Prerequisites
* **OS:** Windows 10 / 11 (64-bit)
* **Compiler:** MSVC (Visual Studio 2022 or later)
* **Build System:** CMake (v3.20+)
* **GPU Driver:** NVIDIA Graphics Card supporting NVENC with the latest drivers installed
