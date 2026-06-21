# 📁 WebFile

**Peer-to-peer file transfer in your browser.** No accounts, no uploads, no servers to run.

All file data flows directly between browsers via WebRTC — nothing goes through a server. When peers are on the same LAN, files stay entirely local.

## Two Versions

| | [`index.html`](index.html) | [`zero-dep/index.html`](zero-dep/index.html) |
|---|---|---|
| **Name** | WebFile | WebFile Zero |
| **Dependencies** | [PeerJS](https://peerjs.com/) CDN (~50KB) | **None** — pure browser APIs |
| **Join flow** | 6-char room code, one step | ~220-char invite code, two-step exchange |
| **Signaling** | PeerJS free cloud relay | Manual code copy-paste |
| **Best for** | Ease of use | Maximum privacy & independence |

Both versions share the same file transfer features, design, and encryption.

## ✨ Features

- **P2P File Transfer** — Direct WebRTC data channels, no server upload
- **Drag & Drop** — Drop files onto the transfer zone or click to select
- **Multi-File Support** — Send multiple files at once to all connected peers
- **Progress Tracking** — Real-time progress bars with transfer speed (KB/s, MB/s)
- **Click to Download** — Received files get a one-click save button
- **LAN Optimized** — Files stay on your local network when peers are nearby
- **Host Rotation** — Automatic host election when the current host leaves
- **Transfer History** — See all sent/received files with status, clearable
- **Single File** — Each version is one self-contained HTML file

## 🚀 Quick Start

### Run locally

```bash
npx -y serve .
```

Then open `http://localhost:3000` in your browser.

## 📖 How It Works

### Standard Version (`index.html`)

1. Enter your name → **Create Room**
2. Share the 6-character room code or the invite link
3. Others paste the code or open the link → instant connection
4. Drop files to send!

```
Browser A  ◄══════ Direct P2P Data ══════►  Browser B
    ▲                                            ▲
    └──────── PeerJS Cloud (signaling) ──────────┘
                   (tiny metadata only)
```

### Zero-Dep Version (`zero-dep/index.html`)

1. Enter your name → **Create Room** → a ~220-char invite code appears
2. Copy & share the invite code (via DM, text, in person, etc.)
3. Other person pastes it → an **answer code** is generated
4. They copy the answer code and send it back to you
5. You paste the answer → **connected!**
6. Additional peers can be invited from within the room via the **Invite** button

```
Browser A  ◄══════ Direct P2P Data ══════►  Browser B
    ▲                                            ▲
    └──── Manual code exchange (one-time) ───────┘
              (no server involved at all)
```

## 🔒 Security

WebRTC mandates encryption — it cannot be disabled:

| Layer | Protocol | What it protects |
|---|---|---|
| Data channels | **DTLS** | All file data is encrypted between peers |
| Key exchange | **DTLS handshake** | Keys negotiated directly between browsers |

**MITM considerations:**
- After connection, all data is encrypted end-to-end. No one (ISP, router, etc.) can intercept your files.
- The signaling phase (PeerJS relay or manual code exchange) is the trust boundary. In the zero-dep version, you control the exchange channel entirely.

## ⚙️ Technical Details

- **Transfer Protocol**: Files are chunked into 64KB pieces, sent sequentially over ordered reliable data channels
- **Flow Control**: Backpressure via `bufferedAmount` — pauses sending when the channel buffer exceeds 1MB
- **Speed Calculation**: Rolling average updated every 500ms
- **Blob Assembly**: Received chunks are accumulated in memory, assembled into a `Blob`, and offered via `URL.createObjectURL()` for download
- **STUN**: Google's public STUN servers (for NAT traversal)
- **TURN**: Not included (LAN and most internet connections work without it)
- **SDP Compression** (zero-dep): JSON + deflate-raw + base64url encoding

## 🌐 Browser Support

| Browser | Standard | Zero-Dep |
|---|---|---|
| Chrome | 80+ | 80+ |
| Edge | 80+ | 80+ |
| Firefox | 80+ | 113+ (needs `CompressionStream`) |
| Safari | 14.1+ | 16.4+ (needs `CompressionStream`) |

## 📄 License

MIT
