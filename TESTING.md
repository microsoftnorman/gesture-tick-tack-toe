# Testing Instructions

## Overview
This project uses a custom browser-based test framework. Tests live in `tests.html` and can be run by opening the file in any modern browser.

## Running Tests

### In Browser (Manual)
Open `tests.html` in Chrome, Edge, or Firefox. Results display immediately with pass/fail counts.

### In CI (Automated)
The GitHub Actions CI workflow runs tests headlessly using Puppeteer:
1. Starts a local HTTP server serving the project files
2. Opens `tests.html` in headless Chrome via Puppeteer
3. Extracts pass/fail counts from the DOM
4. Fails the CI job if any test fails

## Test Framework API

```javascript
// Define a test suite
suite('Suite Name', (container) => {
  // Define individual tests
  test('test description', () => {
    // Assertions
    assert(condition, 'error message');
    assertEqual(actual, expected, 'label');
  }, container);
});
```

## What's Tested

### Unit Tests (Pure Functions)
| Function | What's Verified |
|----------|----------------|
| `classifyGesture(landmarks)` | ROCK/PAPER/SCISSORS/UNKNOWN classification from 21 landmark arrays |
| `isFingerExtended(landmarks, finger)` | Finger extension detection for all 5 fingers |
| `determineWinner(player, computer)` | All 9 matchup outcomes (3×3 grid) |
| `getComputerMove()` | Returns only valid gestures; roughly uniform distribution (chi-squared test) |

### Integration Tests
| Scenario | What's Verified |
|----------|----------------|
| Gesture → Winner pipeline | Landmarks → classify → determine winner, end-to-end |

### Test Data
Synthetic landmarks are generated via `makeLandmarks(config)`:
```javascript
makeLandmarks({ index: true, middle: false, ring: false, pinky: false })
// Creates a 21-landmark array with index finger extended, others curled
```

## Adding New Tests

1. Open `tests.html`
2. Add a new `suite()` block or add `test()` calls to an existing suite
3. Use `makeLandmarks()` to create synthetic hand data
4. Use `assert()` and `assertEqual()` for assertions
5. If testing new pure functions from `index.html`, duplicate the function in `tests.html` (no module system)

## Test Pyramid (Martin Fowler)
```
        ╱  E2E  ╲        ← Manual: real webcam, cross-browser
       ╱─────────╲
      ╱Integration╲      ← Automated: gesture→winner pipeline
     ╱─────────────╲
    ╱  Unit Tests   ╲    ← Automated: classifier, game logic, scoring
   ╱─────────────────╲
```

- **Unit tests**: Fast, deterministic, no browser APIs needed (all in `tests.html`)
- **Integration tests**: Verify the classify→winner pipeline works end-to-end
- **E2E tests**: Manual — play the game with a real webcam across browsers
