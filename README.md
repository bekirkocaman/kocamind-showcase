# KocaMind

**One subscription, many AI models.** A Turkish-first AI assistant that puts chat, image and video generation, an agent, deep research and fully offline on-device AI into a single app.

🌐 [kocamind.com](https://kocamind.com)

> This is a showcase repository. The product source code is private; the open-source part lives in [agent-url-guard](https://github.com/bekirkocaman/agent-url-guard).

## Why

Good AI is split across separate apps and separate subscriptions. KocaMind's goal is to let people finish their work in one place, at a lower price, without giving up quality.

## What's inside

| Area | Features |
|---|---|
| **Modes** | Chat, Agent (asks for approval before actions), Deep Research |
| **Media** | Image and video generation, photo/camera/document attachments, voice messages, read-aloud |
| **Cloud models** | Multiple AI providers behind one backend gateway, with streaming (SSE) answers and a visible "thinking" view |
| **Offline AI** | GGUF models running on the phone through llama.cpp, with a device-compatibility score, model recommendations and a speed test |
| **Offline mode** | Messages queue up without internet and send when the connection returns |
| **Sync** | Chats sync between Android and web under the same account |

## Architecture

```
Android (Kotlin + Jetpack Compose) ─┐
                                    ├──►  Backend (Node.js / Express)  ──►  AI providers
Web (React 19 + Vite, PWA) ─────────┘        │  RAG (Qdrant), memory, files, voice, agent tools
                                             └► Firebase Auth / Firestore / Storage
Android ──► llama.cpp via a small JNI bridge (on-device, no network)
```

- **Native on-device inference:** prebuilt llama.cpp libraries for `arm64-v8a` with a custom JNI bridge. A separate bridge is built per CPU variant (v8, dotprod, dotprod+i8mm) and the fastest one is loaded at runtime.
  Measured on a Samsung SM-S711B with Qwen 2.5 0.5B (Q4_K_M): **~16.6 tokens/s** warm generation, streaming Turkish output.
- **Security by default:** every backend endpoint except health and the model catalog requires a verified Firebase ID token; the `userId` in a request body is never trusted. Firestore rules limit each user to their own data. Agent web tools go through an SSRF guard (open-sourced as [agent-url-guard](https://github.com/bekirkocaman/agent-url-guard)).
- **Operations:** a `/health` endpoint for uptime checks, per-model metrics (calls, error rate, latency, tokens, estimated cost) and per-user daily usage tracking.
- **Privacy:** account deletion has a 60-day grace period, after which a scheduled job purges Firestore data, stored files, RAG documents and the auth user.

## Tech stack

Kotlin · Jetpack Compose · llama.cpp (JNI) · React 19 · Vite · Node.js · Express · Firebase · Qdrant · Vercel

## Author

Built by **Bekir Kocaman**, computer science student at SEEU.
[LinkedIn](https://www.linkedin.com/in/bekirkocaman) · [GitHub](https://github.com/bekirkocaman)
