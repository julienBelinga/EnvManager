# EnvManager – P2P Encrypted .env Secrets Manager

EnvManager is an open‑source, peer‑to‑peer secrets manager designed for development teams. It allows teams to securely share and synchronize `.env` files across multiple machines without storing any secret data on a central server — not even encrypted.

The project is self‑hostable, zero‑knowledge by design, and focuses on strong security, modern cryptography, and real‑world P2P networking.

---

## Project goals

- Share `.env` files securely within small development teams (2–10 people)
- Never store secrets on a server (zero storage server model)
- End‑to‑end encryption for all secret data and metadata
- Peer‑to‑peer synchronization with distributed source of truth
- Versioning of secrets
- Developer‑friendly tooling (CLI, VS Code, Vim)
- Linux & macOS support (Windows planned in V2)
- Fully open‑source and self‑hostable

---

## How it works (high level)

1. Each user owns a cryptographic key pair.
2. Each team owns a symmetric **Team Key**.
3. Each `.env` file version is encrypted locally using a **Data Encryption Key (DEK)**.
4. The DEK is encrypted using the Team Key.
5. Encrypted secrets are shared directly between team members using P2P connections (WebRTC).
6. A minimal "blind" server is used only for peer discovery and connection signaling.
7. Secrets are stored encrypted on local machines and replicated to multiple peers for availability.

The server never receives:

- secret contents
- filenames
- encryption keys
- project metadata

Only opaque identifiers and encrypted payloads transit through the server.

---

## MVP features

- Create teams (up to 10 members)
- Multiple `.env` files per team (per repository / service)
- P2P file transfer and synchronization
- Distributed source of truth (replication across machines)
- Secret versioning
- CLI interface
- VS Code extension
- Linux and macOS support
- NAT traversal with STUN + TURN fallback
- Encrypted local storage
- Security‑critical core written in Rust
- Networking and orchestration written in Go

---

## Security model

- Zero‑knowledge server
- End‑to‑end encryption (application‑level + transport‑level)
- Envelope encryption (DEK + Team Key)
- Private keys never leave the user machine
- Encrypted local storage
- Memory zeroization for sensitive material (Rust core)

---

## Lexicon

### DEK (Data Encryption Key)

A symmetric key generated per secret version to encrypt a `.env` file. It is then encrypted using the Team Key.

### Team Key

A symmetric key shared securely among team members. Used to encrypt DEKs.

### NAT (Network Address Translation)

A network mechanism used by routers that prevents direct inbound connections to most personal computers. Makes P2P networking harder.

### STUN

A protocol used to discover a machine's public IP and port in order to establish peer‑to‑peer connections through NAT.

### TURN

A relay server used when direct P2P connections fail. It forwards encrypted traffic between peers without decrypting it.

### WebRTC

A real‑time communication framework used to establish encrypted peer‑to‑peer connections and data channels.

### Signaling Server

A minimal server used only to exchange connection metadata (SDP/ICE) between peers to establish WebRTC connections.

### Zero‑knowledge

An architecture where the server cannot access, decrypt, or understand any sensitive user data.

---

## Repository architecture (monorepo)

This project uses a single monorepo to simplify development, versioning and contributions.

```
envmgr/
  apps/
    envd/            # Go daemon: P2P networking, replication, API
    envctl/          # Go CLI client
    server/    # Go signaling server (zero-knowledge)
  core/
    crypto-core/     # Rust crate: cryptography & secure storage
  extensions/
    vscode/          # VS Code extension
    vim/             # Vim / Neovim plugin
  docs/
  deploy/
    docker/
```

---

## Technology stack

- Go – networking, daemon, CLI, signaling server
- Rust – cryptography core, secure memory handling
- WebRTC (pion)
- STUN / TURN
- Unix domain sockets (local API)

---

## Project status

Currently in early development (MVP phase).

Planned next steps:

- P2P prototype
- Crypto core integration
- Local encrypted storage
- Team management
- CLI
- VS Code extension

---

## Disclaimer

This project is experimental and not yet audited. Do not use in production until proper security reviews are completed.
