# Copilot Instructions

## Project Overview
This is a browser-based Rock Paper Scissors game driven entirely by hand gestures captured through the user's webcam. No mouse clicks or keyboard input required during gameplay.

## Architecture
- **Single-file app**: Everything lives in `index.html` — HTML, CSS, and JavaScript inlined for zero-build-tool portability.
- **Test suite**: `tests.html` contains unit and integration tests using a custom mini test framework (no npm dependencies).
- **No server**: Runs entirely client-side. Open `index.html` in a browser.
- **No build step**: No bundlers, transpilers, or package managers.

## Technology Stack
| Layer | Technology |
|-------|------------|
| Hand Tracking | MediaPipe Hands v0.4 (CDN, WASM runtime) |
| Rendering | HTML5 Canvas + CSS3 animations |
| Audio | Web Audio API |
| Language | Vanilla ES6+ JavaScript (no TypeScript) |
| Camera | getUserMedia API |

## Key Patterns

### Gesture Classification
- Uses MediaPipe's 21-landmark hand model
- Finger extension detected by comparing fingertip y-coordinate vs PIP joint y-coordinate
- Thumb uses x-distance from wrist instead of y-coordinate
- Gestures: ROCK (all curled), PAPER (4+ extended), SCISSORS (index + middle only)
- Gesture must be stable for 3+ consecutive frames before being accepted

### Game State Machine
```
LOADING → MENU → COUNTDOWN → CAPTURE → RESULT → COUNTDOWN (loop)
                                                → MATCH_END → MENU
```
States are managed via `gameState.current` and `transitionTo()` / `onStateEnter()`.

### Pure Functions (testable)
These functions are exported to `window` for testing:
- `classifyGesture(landmarks)` → `{ gesture, confidence }`
- `isFingerExtended(landmarks, finger)` → `boolean`
- `determineWinner(player, computer)` → `'player' | 'computer' | 'draw'`
- `getComputerMove()` → `'ROCK' | 'PAPER' | 'SCISSORS'`
- `isMatchOver()` → `boolean`

### Audio
Uses Web Audio API oscillators for sound effects — no audio files. The `audioEngine` object handles all sounds.

## Code Style
- **Vanilla ES6+** — no frameworks, no jQuery, no TypeScript
- **camelCase** for variables and functions
- **UPPER_SNAKE_CASE** for constants
- **Single IIFE** wraps all game code to avoid global pollution (only test-facing functions exported to `window`)
- Prefer `const` over `let`, never use `var`
- Use template literals for string interpolation
- Use `===` strict equality everywhere

## Security Rules
- **No `eval()`** or `Function()` constructor
- **No dynamic `innerHTML`** with user/external data
- **No external network requests** — all processing stays client-side
- Camera stream never leaves the browser
- Only CDN dependencies: `@mediapipe/hands` and `@mediapipe/camera_utils`

## Performance Guidelines
- Camera resolution: 640×480 (not HD)
- MediaPipe model complexity: 0 (Lite)
- Use `requestAnimationFrame` for animations, not `setInterval`
- Debounce gesture classification (only on stable detection)
- Keep total project size under 15 MB

## Testing
- Tests live in `tests.html` — open in browser to run
- Tests use a custom mini framework (`suite()`, `test()`, `assert()`, `assertEqual()`)
- Pure functions are duplicated in `tests.html` (since there's no module system)
- Test categories: gesture classifier, finger extension, game logic (determineWinner), computer move randomness, integration pipeline
- When adding new pure logic, add corresponding tests in `tests.html`

## Browser Support
- Chrome 90+, Edge 90+, Firefox 100+
- Requires WebRTC (getUserMedia) and WASM support
