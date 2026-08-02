# API Testing — WebSocket (XQA.io)

**Module:** WebSocket Testing (XQA.io)
**Endpoint:** `ws://demo.xqa.io/socket`
**Precondition:** Site is launched, the WebSocket Testing component is open

## Note on Approach

Unlike REST (request → response, stateless), WebSocket is a persistent, bidirectional connection established via a handshake. This changes what "testing" means in practice: individual request/response pairs still matter, but so does connection lifecycle (connect/disconnect/reconnect), unsolicited server-initiated messages (server push — no equivalent in REST), message ordering, and how the server handles malformed input over an open connection rather than a single rejected HTTP call.

## Test Cases

| # | Test Name | Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-----------|--------------|------------------|----------------|-----------|-----------|
| 1 | Successful connection | 1. Click Connect. 2. Observe status indicator and log. | Status changes to "Connected" (green), log records the connection event, server pushes a welcome message unprompted | Status changed to "Connected", log showed connect entry, server pushed `{"type":"welcome","message":"Welcome to XQA WebSocket Demo"}` ~1s later | Pass | `ws://demo.xqa.io/socket` |
| 2 | Disconnect via toggle button | 1. While connected, click Disconnect (same button, relabeled from Connect). 2. Observe status and log. | Status returns to "Disconnected", log records the disconnect event | Status returned to "Disconnected", log showed "Disconnected from server" | Pass | — |
| 3 | Reconnect after disconnect | 1. Disconnect. 2. Click Connect again. 3. Observe log. | New connection established, welcome message pushed again; prior session's log entries remain visible | New connection established, welcome message received again; previous session's log entries were retained (not cleared) | Pass | — |
| 4 | Ping / Pong | 1. Send `{"action": "ping"}` via Quick Message. 2. Observe response. | Server responds with a pong confirmation | Server responded `{"action":"pong","timestamp":<unix_ms>}` after ~1s | Pass | `{"action": "ping"}` |
| 5 | Subscribe to channel | 1. Send `{"action": "subscribe", "channel": "updates"}`. 2. Observe response. | Server confirms the subscription | Server responded `{"status":"subscribed","channel":"updates"}` after ~1s | Pass | `{"action": "subscribe", "channel": "updates"}` |
| 6 | Chat message echo | 1. Send `{"type": "chat", "message": "Hello!"}`. 2. Observe response. | Server echoes the message back with a delivery confirmation | Server responded `{"echo":{"type":"chat","message":"Hello!"},"received":true}` instantly (no delay, unlike ping/subscribe) | Pass | `{"type": "chat", "message": "Hello!"}` |
| 7 | Server-initiated push message | 1. While connected, click "Simulate Server Push". 2. Observe log. | A message appears in the log without any client-initiated request | Received `{"type":"notification","data":"New user joined the channel"}` unprompted | Pass | — |
| 8 | Send disabled while disconnected | 1. Ensure status is "Disconnected". 2. Attempt to type in the message input and use Send. | Input field and Send button should be disabled, preventing message submission with no active connection | Input field was disabled; text could not be entered and Send was not usable | Pass | — |
| 9 | Quick Message buttons replace input content | 1. Click one Quick Message button. 2. Click a different Quick Message button. 3. Observe the input field. | Second click replaces the field's content with the new message, not appended to the first | Field content was replaced correctly on each click | Pass | Quick Message buttons |
| 10 | Keyboard Tab navigation through controls | 1. Use Tab key to move focus across Connect/Disconnect, checkboxes, and Quick Message buttons. 2. Observe focus indicator. | Focus should move sequentially, one control at a time, with a visible focus indicator | Focus moved sequentially through controls as expected | Pass | — |
| 11 | Malformed JSON handling (unquoted keys) | 1. Manually type `{action: ping}` (invalid JSON — unquoted key) into the input. 2. Click Send. 3. Observe response. | Server should reject the message or return a descriptive parse error (e.g. `{"error":"Invalid JSON"}`) | Server did not reject or flag the input — it echoed the raw string back: `{"echo":"{action: ping}","received":true}` | **Fail** | `{action: ping}` |
| 12 | Plain-text (non-JSON) input handling | 1. Type a plain string, e.g. `helli`, with no JSON structure at all. 2. Click Send. 3. Observe response. | Server should reject non-JSON input with a descriptive error | Server accepted it silently and echoed it back: `{"echo":"helli","received":true}` | **Fail** | `helli` |
| 13 | Unknown action value | 1. Send valid JSON with an unrecognized action, e.g. `{"action": "foobar"}`. 2. Observe response. | Server should distinguish an unrecognized action from a successful one — e.g. return an error like `{"error":"Unknown action"}` | Server treated it identically to unparseable input — echoed it back: `{"echo":{"action":"foobar"},"received":true}`, with `"received":true` implying success | **Fail** | `{"action": "foobar"}` |
| 14 | Multiple concatenated JSON objects in one message | 1. Manually type two or three valid JSON objects back to back with no separator, e.g. `{"action": "subscribe", "channel": "updates"} {"action": "ping"}`. 2. Click Send. 3. Observe response. | Server should reject the message as invalid (not parseable as a single JSON value) | Server did not reject it — the entire concatenated string was echoed back as one raw string, same as test #11/#12 | **Fail** | `{"action": "subscribe", "channel": "updates"} {"action": "ping"}` |
| 15 | Message type field consistency across message kinds | 1. Compare the top-level key used to identify message type across all responses received so far (welcome, pong, subscribed, echo, notification). | The same key should identify "what kind of message this is" across all server messages, for predictable client-side parsing | Four different conventions observed: `"type"` (welcome, notification), `"action"` (pong), `"status"` (subscribed), and no type-identifying key at all (echo — only `"echo"` + `"received"`) | **Fail** | — |

## Bug Reports

### Bug #1: Server does not validate incoming messages — malformed and unrecognized input is silently accepted
- **Severity:** Medium
- **Module:** WebSocket Testing
- **Related Test Cases:** #11, #12, #13, #14
- **Precondition:** Site is launched, the WebSocket Testing component is open, connection is active
- **Expected Result:** The server should distinguish between valid, recognized messages and invalid/unrecognized ones — returning a descriptive error (e.g. `{"error":"Invalid JSON"}` or `{"error":"Unknown action"}`) so the client knows its message wasn't understood
- **Actual Result:** Any input — unquoted-key JSON, plain non-JSON text, multiple concatenated JSON objects, or valid JSON with an unrecognized action — is uniformly wrapped in an echo response with `"received":true`, giving the impression of successful processing regardless of whether the input was actually understood
- **Steps to Reproduce:**
  1. While connected, manually type `{action: ping}` (missing quotes around the key) into the message field and click Send — observe it gets echoed back as a raw string instead of rejected.
  2. Send a plain string with no JSON structure, e.g. `helli` — same silent-echo behavior.
  3. Send a valid JSON object with an unrecognized action, e.g. `{"action": "foobar"}` — echoed back with `"received":true`, no indication the action was unrecognized.
  4. Manually type two valid JSON objects concatenated with no separator and send — the whole string is echoed as one raw string instead of being rejected as unparseable.
- **Status:** New

### Bug #2: Inconsistent "message type" key across different server responses
- **Severity:** Low
- **Module:** WebSocket Testing
- **Related Test Case:** #15
- **Precondition:** Site is launched, the WebSocket Testing component is open, connection is active
- **Expected Result:** All server-originated messages should identify their kind through a single, consistent key (e.g. always `"type"`), so a client can reliably branch on message type without special-casing each response
- **Actual Result:** Four different conventions are used depending on the message: `"type"` for welcome/notification, `"action"` for pong, `"status"` for subscribed, and no type-identifying key at all for echo responses (only `"echo"` and `"received"`)
- **Steps to Reproduce:**
  1. Connect and note the welcome message uses `"type":"welcome"`.
  2. Send `{"action":"ping"}` — response uses `"action":"pong"` instead of `"type"`.
  3. Send `{"action":"subscribe","channel":"updates"}` — response uses `"status":"subscribed"`.
  4. Send `{"type":"chat","message":"Hello!"}` — response has no type-identifying key at all, only `"echo"` and `"received"`.
  5. Trigger a server push — response uses `"type":"notification"`.
  6. Compare the top-level keys across all five responses.
- **Status:** New

## Observations (not filed as bugs)

- **Inconsistent response latency:** ping and subscribe responses arrive with a ~1s delay, while chat echo responses return with no observable delay (same timestamp as the request). Not confirmed as a defect, but worth flagging as a pattern to watch if response-time assertions are added later.
- **Message log is not cleared on reconnect:** entries from a previous connection session remain visible in the log after disconnecting and reconnecting. Could be intentional (log = full session history) or an oversight — noted as-is rather than assumed either way.
- **Protocol uses `ws://`, not `wss://`:** the connection is unencrypted. Expected for a public demo/sandbox, but would be a real concern on a production service.

## Reflections

- This was a first pass at WebSocket testing, following manual exploration before formal test-case writing — mirrors the approach used in REST API Playground testing (explore first, then document).
- The core mental shift from REST: testing here isn't just "does this one request get the right response," it's also connection lifecycle (connect/disconnect/reconnect), server-initiated push messages with no client request behind them, and how malformed input behaves over a persistent connection rather than as a single rejected HTTP call.
- The most significant finding — lack of input validation (Bug #1) — echoes the REST practice conclusion: robustness under bad input needs to be actively probed, it's rarely covered by only testing the happy path.
- The inconsistent message-type key (Bug #2) is the same category of issue as the REST Playground's Bug #2 (inconsistent field sets between `/api/users` and `/api/users/1`) — a recurring pattern of schema inconsistency across this practice site's mock APIs.