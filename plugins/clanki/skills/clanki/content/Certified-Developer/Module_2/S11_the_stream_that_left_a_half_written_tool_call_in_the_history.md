# The stream that left a half-written tool call in the history

> **Screen:** S11 · **Section:** Streaming Responses
>
> **Category:** Watch Out Streaming Responses · 5 min

> **Setup**
>
> *A streamed response can look fine on screen and still corrupt the next request. The text rendered, the user saw an answer, and the handler appended the turn to history. What the handler did not catch was that the stream dropped mid-block, so the tool\_use call it stored was missing half its input. The next request fails validation, and the error points at the next turn and not the stream that caused it.*

### Postmortem: partial tool\_use block committed to history after a dropped stream

An agent used streaming so its operators could watch responses generate in real time. The handler accumulated content\_block\_delta events and appended the assistant turn to history when its read loop ended. In testing on a fast local connection, streams always ran to completion, so the loop always ended at message\_stop and the stored turns were always complete.

In production, a network blip ended one stream after the tool\_use block had opened and received part of its JSON input, but before content\_block\_stop. The read loop ended the same way it always had, so the handler appended the turn: an assistant turn containing a tool\_use block whose input string was truncated JSON. The operator saw a partial answer and retried. The retry request included that corrupted turn in history, and the API rejected it with a validation error pointing at the malformed tool\_use block.

The team spent an afternoon inspecting the schema and the retry logic, because the error surfaced on the retry request. However, the actual cause was upstream: the handler treated 'the read loop ended' as equivalent to 'the message is complete,' and those are not the same.

> **What to Watch Out for**
>
> A stream ending is not the same as a message completing. Only message\_stop means the message is whole. If your handler commits a turn whenever its read loop exits, an interrupted stream writes a half-built block into history, and the failure shows up on the next request rather than the one that caused it. Gate the history append on message\_stop, discard the partial turn on interruption, and retry from the last complete turn. When a tool-use error appears on a retry, check whether the prior turn was assembled from a stream before you touch the schema.
