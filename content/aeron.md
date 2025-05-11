+++
title = "Aeron client notes"
date  = "2025-05-11"
description = "Notes on high performance messaging library Aeron."
[taxonomies]
tags = ["java"]
+++

Notes on [Aeron](https://github.com/aeron-io/aeron), a high-thorughput messaging system.

- Structure:
    - Client library included in application
        - Java, C/C++, C#, Rust (community) supported
        - Communicates with media driver via ring buffers in memory-mapped command and control file
    - Media driver runs in separate JVM process
        - Send/receive UDP or IPC
        - Owns the memory-mapped file for client comms
        - Client language agnostic
    - Archive service
        - Seperate process that persists messages to disk (e.g. for replays or regulatory purposes)
    - Cluster
        - Distributed archive
        - Raft consensus

- Uses [Agrona](https://github.com/aeron-io/agrona) utilitis and ds
 
