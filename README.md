# Estuary Snap Spectacles Demo

A demonstration Lens Studio project showcasing AI-powered character interactions on Snap Spectacles using the Estuary SDK. This project features a friendly cloud character that can follow you around and engage in natural voice conversations.

## Features

- **Real-Time Voice Conversations** — Talk naturally with AI characters using Spectacles' built-in microphone
- **Voice Responses** — Hear AI characters respond with synthesized speech
- **Vision Intent Detection** — Say things like "what do you think of this?" and the AI can see through the camera
- **Interactive 3D Characters** — The cloud character follows you around in AR space
- **Action System** — AI can trigger in-scene actions (e.g., "follow me", "stop following")
- **Streaming Responses** — Low-latency text and audio streaming for natural conversations
- **Conversation Persistence** — Your conversation history is automatically saved

## Requirements

### Hardware
- **Snap Spectacles (5th Generation)** — Required for voice features and AR interaction

### Software
- **Lens Studio 5.15+** — Download from [Snap AR](https://ar.snap.com/lens-studio)
- **Estuary Account** — Get your API key at [app.estuary-ai.com/api-keys](https://app.estuary-ai.com/api-keys)

### Assets (Included)
- `RemoteServiceGateway.lspkg` — Snap's package for microphone and audio output
- `Cloud.lspkg` — Animated cloud character asset

## Quick Start

### 1. Open in Lens Studio

1. Launch Lens Studio 5.15 or later
2. Open this project by selecting `Estuary SDK.esproj`
3. The project will load with the demo scene

### 2. Configure Credentials

1. Find the `EstuaryCredentials` SceneObject in the scene hierarchy
2. Configure the following in the Inspector:
   - **API Key** — Your Estuary API key from the [API Keys page](https://app.estuary-ai.com/api-keys)
   - **Character ID** — UUID of your AI character (click the three-dot menu on your character, then "Copy Character ID")
   - **Server URL** — `https://api.estuary-ai.com` (default)
   - **Debug Mode** — Enable for development logging

### 3. Verify Audio Components

The demo project already includes the required audio components from `RemoteServiceGateway.lspkg`:

- **MicrophoneRecorder** — Handles voice input from the Spectacles microphone
- **DynamicAudioOutput** — Plays AI voice responses through the Spectacles speakers
- **InternetModule** — Enables WebSocket connections for real-time communication

These should already be connected to `EstuaryVoiceConnection` in the scene. If you need to reconfigure them, check the Inspector panel for the `EstuaryVoiceConnection` script component.

### 4. Deploy to Spectacles

1. Click **Publish Lens** in Lens Studio
2. Push to your Spectacles device via the Snap app
3. Launch the Lens on your Spectacles and start talking!

## Project Structure

```
estuary-snap-spectacles-demo/
├── Assets/
│   ├── estuary-lens-studio-sdk/    # Estuary SDK for Lens Studio
│   │   ├── src/
│   │   │   ├── Components/         # High-level components
│   │   │   │   ├── EstuaryManager.ts
│   │   │   │   ├── EstuaryCharacter.ts
│   │   │   │   ├── EstuaryMicrophone.ts
│   │   │   │   ├── EstuaryActionManager.ts
│   │   │   │   ├── EstuaryCredentials.ts
│   │   │   │   └── VisionIntentDetector.ts
│   │   │   ├── Core/               # Low-level client & config
│   │   │   │   ├── EstuaryClient.ts
│   │   │   │   ├── EstuaryConfig.ts
│   │   │   │   └── EstuaryEvents.ts
│   │   │   ├── Models/             # Data types
│   │   │   └── Utilities/          # Helper functions
│   │   └── Examples/               # Example implementations
│   │       ├── EstuaryVoiceConnection.ts   # Main auto-connect script
│   │       ├── EstuaryCamera.ts            # Camera capture handling
│   │       └── ExampleCharacterActions.ts
│   ├── Scripts/
│   │   └── CloudyFollowController.ts    # Cloud follow behavior
│   ├── Cloud.lspkg/                     # Cloud 3D character
│   └── Scene.scene                      # Main scene file
├── Packages/
│   └── RemoteServiceGateway.lspkg       # Snap audio package
├── Support/
│   └── StudioLib.d.ts                   # Lens Studio type definitions
├── Estuary SDK.esproj                   # Project file
├── tsconfig.json                        # TypeScript configuration
└── jsconfig.json                        # JavaScript configuration
```

## Usage

### Voice Interaction

Once deployed, simply speak to your AI character:

```
You: "Hey, how are you doing?"
AI:  "I'm doing great! It's wonderful to see you..."

You: "Can you follow me?"
AI:  "Sure, I'll follow you around!" [Cloud starts following]

You: "What do you think of this painting?"
AI:  [Camera captures image] "That's a beautiful piece of art..."
```

### Action System

The demo includes an action system that lets the AI control scene elements. Define actions in your character's prompt on the Estuary dashboard:

```xml
<action name="follow_user" />
<action name="stop_following_user" />
```

Subscribe to actions in your scripts:

```typescript
import { EstuaryActions } from "estuary-lens-studio-sdk/src/Components/EstuaryActionManager";

// Subscribe to action events
EstuaryActions.on("follow_user", () => {
    // Start following logic
});

EstuaryActions.on("stop_following_user", () => {
    // Stop following logic
});
```

### Vision Intent Detection

Enable natural language camera activation for phrases like:
- "What do you think of this?"
- "Can you see what I'm looking at?"
- "Tell me about this vase"

Configure in `EstuaryVoiceConnection`:
- `enableVisionIntentDetection`: true/false
- `visionConfidenceThreshold`: 0.0-1.0 (default: 0.7)

## Configuration Options

### EstuaryCredentials

| Property | Description |
|----------|-------------|
| `apiKey` | Your Estuary API key |
| `characterId` | UUID of the AI character |
| `serverUrl` | API endpoint (default: `https://api.estuary-ai.com`) |
| `userId` | Optional persistent player ID |
| `debugMode` | Enable verbose logging |

### CloudyFollowController

| Property | Description | Default |
|----------|-------------|---------|
| `followDistance` | Distance in front of camera (cm) | 60 |
| `heightOffset` | Vertical offset from camera | 10 |
| `positionLerpSpeed` | Position smoothing (higher = snappier) | 6 |
| `rotationLerpSpeed` | Rotation smoothing | 8 |
| `enableLookAt` | Cloud faces the user | true |

### Audio Settings

| Property | Description | Default |
|----------|-------------|---------|
| `audioSampleRate` | TTS playback rate | 16000 Hz |
| Recording | Mic input rate | 16000 Hz |
| Playback | Voice output rate | 16000 Hz |

## Troubleshooting

### No Voice Responses

1. Ensure your character has a **Voice Preset** configured in the Estuary dashboard
2. Verify `DynamicAudioOutput` is properly set up with an AudioComponent
3. Check that the Audio Track asset is assigned

### Microphone Not Working

1. Confirm `MicrophoneRecorder` SceneObject is connected
2. Check that microphone permissions are granted on device
3. Enable `debugMode` to see audio streaming logs

### Connection Timeout

1. Verify your API key is correct and active
2. Check `serverUrl` matches `https://api.estuary-ai.com`
3. Ensure your Spectacles have internet connectivity
4. The SDK sends keepalive pings automatically

### Character Not Responding

1. Confirm `characterId` matches a character in your dashboard
2. Check the Estuary dashboard for quota limits
3. Enable `debugMode` for detailed connection logs

### Audio Feedback Loop

If the AI keeps responding to itself or you hear echoing:

1. The microphone may be picking up the AI's voice output
2. Mute the microphone while the AI is speaking, or move to a quieter environment
3. Consider adjusting the microphone sensitivity or speaker volume
4. The SDK includes interrupt detection that should stop playback when you speak

## EncounterDemo

`Assets/Scripts/EncounterDemo.ts` is a `@component` Behavior that
exercises the new "Encounter" feature end-to-end on Spectacles. An
Encounter is a stateless, prompt-driven, bounded 2-character A2A streaming
session: you specify two character IDs, a topic prompt, and a turn budget,
and the backend streams alternating turns (with optional TTS) over the
existing `/sdk` Socket.IO connection. When it ends, it ends — no DB writes,
no memory.

See `SDK_CONTRACT.md` (`§Features > encounter` and `§REST API — Encounter`)
for the canonical contract.

### Add to a scene

1. Drop `EncounterDemo.ts` onto a SceneObject in your scene. Make sure
   another bootstrap script (typically `SimpleAutoConnect`) has already
   authenticated `EstuaryManager.instance` — `EncounterDemo` polls
   `EstuaryManager.instance.connectionState` and waits for `Connected`.
2. Inspector wiring:
   - **`characterAId` / `characterBId`** — UUIDs of two characters owned by
     the API key configured on `EstuaryCredentials`. Both must satisfy
     `Agent.user_id == api_key_user_id` server-side; mismatched IDs return
     a non-enumerating HTTP 404.
   - **`prompt`** — Topic/scene fed to both characters (1..2000 chars).
   - **`maxTurns`** — One message = one turn. Server hard-caps at 20
     (HTTP 422 if exceeded).
   - **`voice`** — Toggle TTS playback. When true, both `audioOutputA*`
     SceneObjects must be wired (see below).
   - **`textComponent`** — Optional `Component.Text` that renders the
     transcript as it arrives.
   - **`autoStart`** — When true, fires on awake. When false, call
     `EncounterDemo.start()` from another script (e.g. a button).

### Voice playback wiring (when `voice = true`)

The Encounter feature emits `encounter_voice` events with `speaker: "a"` or
`"b"`. The Behavior dispatches each PCM frame to one of two
`DynamicAudioOutput` instances from `RemoteServiceGateway.lspkg`. To wire
voice:

1. Create two SceneObjects, each with a `DynamicAudioOutput` script and a
   sibling `AudioComponent` (Snap's standard audio playback rig — see the
   "Verify Audio Components" section above for the canonical setup).
2. Drag the speaker-A SceneObject onto `audioOutputAObject`, and the
   speaker-B SceneObject onto `audioOutputBObject`.
3. The Behavior decodes `voice.audio` (base64 PCM 24 kHz mono) via Lens
   Studio's native `Base64.decode(...)` and feeds the resulting
   `Uint8Array` to `addAudioFrame(pcmBytes, 1)`. The exact decode + frame-
   feed pattern is lifted from
   `Assets/estuary-lens-studio-sdk/Examples/EstuaryVoiceConnection.ts:404-415`
   — refer to that file as the canonical reference.

The `DynamicAudioOutput` reference is typed as `any` in the `@input` because
its TypeScript surface lives in `RemoteServiceGateway.lspkg` and is not part
of the Estuary SDK's exports. The Behavior duck-types the component by
checking for the `addAudioFrame` method.

### What you should see

With `voice = false` and `maxTurns = 6`, the Logger panel prints six lines
of `[EncounterDemo] (N) [A] ...` / `[EncounterDemo] (N) [B] ...`
followed by `[EncounterDemo] Encounter ended: reason=max_turns_reached
turns=6`. With `voice = true`, audio plays alternately from
`audioOutputAObject` and `audioOutputBObject`.

### Known limitations (MVP)

- Characters with `tts_provider="inworld"` (the default) silently fall back
  to the default ElevenLabs voice (`hpp4J3VqNfWAUOO0d1Us`, "Bella"); generic
  gateway-side TTS dispatch is out of scope at MVP.
- The encounter is NOT cancelled on disconnect; if you leave mid-encounter,
  the server runs to completion and emits to a 0-subscriber room. Do not
  rely on a reconnecting client receiving the prior turns.

## Additional Resources

- **Estuary Documentation** — [docs.estuary-ai.com](https://docs.estuary-ai.com)
- **Estuary Dashboard** — [app.estuary-ai.com](https://app.estuary-ai.com)
- **Lens Studio Docs** — [docs.snap.com/lens-studio](https://docs.snap.com/lens-studio)

## Support

- **Discord** — [discord.gg/E5EatETMmc](https://discord.gg/E5EatETMmc)
- **Documentation** — [docs.estuary-ai.com](https://docs.estuary-ai.com)

## License

This project is licensed under the MIT License. See the [LICENSE](Assets/estuary-lens-studio-sdk/LICENSE) file for details.

---

Built by [Estuary AI](https://estuary-ai.com)
