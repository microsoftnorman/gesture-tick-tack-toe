# Paper Rock Scissors — Webcam Gesture Game

## Overview
A browser-based Rock Paper Scissors game driven entirely by hand gestures captured through the user's webcam. No mouse clicks or keyboard input required during gameplay.

## Core Requirements

### Gesture Recognition
- Detect three hand gestures in real-time via webcam: Rock (fist), Paper (open hand), Scissors (two fingers extended)
- Use **MediaPipe Hands** for hand landmark detection (21 key points per hand)
- Classify gestures from landmark geometry (finger extension states)
- Minimum detection confidence: 70%

### Gameplay
- Player vs. Computer mode
- Countdown timer (3-2-1-Shoot!) before each round
- Computer randomly selects its move after countdown
- Best-of-5 match format
- Score tracking per session

### Technology Stack
- **MediaPipe Hands** (via @mediapipe/hands or CDN) — hand landmark detection
- **TensorFlow.js** — runtime for MediaPipe models
- Vanilla HTML/CSS/JavaScript — no heavy frameworks
- Web browser Camera API (getUserMedia)

### Performance
- Target: 30 FPS gesture detection on average hardware
- Lightweight model suitable for integrated laptop webcams
- No server required — runs entirely client-side

### User Experience
- Live webcam feed displayed on screen
- Visual feedback showing detected gesture
- Animated countdown before each round
- Clear win/lose/draw result display
- Sound effects for countdown and results
- Fun, colorful, arcade-style UI

## Non-Functional Requirements
- Works in Chrome, Edge, Firefox (modern browsers)
- No installation — single HTML file or simple static site
- Responsive layout (desktop-first)
- Accessible camera permission prompt
