---
title: "Mermaid Test"
date: 2025-09-29T22:10:00Z
draft: false
tags: ["test", "mermaid"]
---

# Mermaid Test Page

This is a simple test to verify Mermaid diagrams are rendering correctly.

## Simple Flowchart

```mermaid
graph TD
    A[Start] --> B{Is it working?}
    B -->|Yes| C[Great!]
    B -->|No| D[Debug needed]
    C --> E[End]
    D --> E[End]
```

## Sequence Diagram

```mermaid
sequenceDiagram
    participant A as Alice
    participant B as Bob
    A->>B: Hello Bob, how are you?
    B-->>A: Great thanks!
```

If you can see the diagrams above, Mermaid is working correctly!
