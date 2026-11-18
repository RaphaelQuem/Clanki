# Streaming responses and handling partial output without corrupting state

> **Screen:** S10 · **Section:** Streaming Responses
>
> **Category:** Teaching Streaming Responses · 16 min

Every request so far has waited for the whole response to arrive before doing anything with it. That's fine, until the response is long, or a user is sitting there staring at a blank screen. Streaming sends the response in pieces, sending them along as the model generates them. That makes things feel faster, but it also gives your code a new job: now you are tasked with assembling the final content yourself based on the series of outputs, and you need to be prepared if the series stops early.

## What streaming changes about the response

In a non-streamed request, the API hands you one complete message with every content block, fully formed. In a streamed request, the API instead sends a series of events that describe the message *as it's being built*. Your code listens to that series and reassembles the blocks. The message you end up with is identical to what a non-streamed call would have given you, but the difference is that *you* have to assemble the pieces, and *you* decide what to do if the events stop before the message is finished.

It helps to know what's not happening: the model isn't holding some live object open for you. Each event is its own small message describing a single change, a block started, some text or input got added to it, a block finished, the whole message finished. Your handler takes each event and applies it to the partial state it's been building up.

## The event sequence, and what your handler does with each

| Event | What it signals | What your handler does |
| --- | --- | --- |
| message\_start | A new message is beginning. Carries the message shell with empty content and initial usage. | Set up an empty content array to collect blocks in. |
| content\_block\_start | A new content block is opening, with its type (text, tool\_use, or thinking) and index. | Make a slot at that index for the named block type. A tool\_use block opens with its name and id, but no input yet. |
| content\_block\_delta | An incremental piece of one block: a text fragment, a fragment of JSON input for a tool call, or a thinking fragment. | Append the fragment to the block at that index. Tool-call inputs arrive as a partial JSON string spread across several deltas, you can't parse them until the block closes. |
| content\_block\_stop | The block at this index is complete. | Finalize the block. For a tool\_use block, this is the first moment the accumulated JSON input is complete enough to parse. |
| message\_delta | Top-level changes to the message: the stop\_reason and final usage counts. | Record the stop\_reason. It tells you whether the model finished or stopped for some other reason. |
| message\_stop | The stream is complete. | The assembled content array is now the finished message. From here, treat it exactly like a non-streamed response. |

## The rule that keeps your state from getting corrupted: don't act on a partial block

The tool\_use block is the one to watch. Its input shows up as a partial JSON string spread across many content\_block\_delta events, and that string *isn't* valid JSON until content\_block\_stop closes the block. If your code tries to parse the input or run the tool before the block closes, it either chokes on malformed JSON or runs with half the arguments missing. So, the rule is simple: collect the deltas, and act only after content\_block\_stop for that block.

The same discipline applies when you add a streamed assistant turn to your conversation history. Add it only after message\_stop, with every block fully assembled. A turn built from a stream that got cut off partway is incomplete, and the tool\_use pairing rules will reject your next request if a half-built tool\_use block ends up in the history.

## When the stream stops early

Streams sometimes fail in the middle. A dropped network connection, a timeout, or a client disconnect can end the event series before message\_stop arrives. The failure that really bites is treating whatever you've collected so far as if it were complete. A partial text block shown to a user is just a cosmetic glitch and a partial tool\_use block written into history is a structural problem that corrupts the next turn.

-   **Track completion on purpose.** A turn is usable only once message\_stop has arrived. Until then, treat what you've accumulated as provisional.
-   **On an interrupted stream, throw away the partial assistant turn** instead of saving it to history, then retry the request. Committing a half-built turn is exactly what breaks the following request.
-   **Check the stop\_reason from message\_delta** before you continue a loop. A stop\_reason of tool\_use means your assembled tool calls are ready to run; any other value means you're on a different path, not the tool path.

**Handles well**  
Long responses and user-facing interfaces where showing output as it generates removes the blank-screen wait.

**Adds cost or complexity**  
You assemble blocks yourself, you must not act on partial blocks, and you must handle mid-stream interruption explicitly.

**Use a different approach**  
For short responses or backend jobs where no one is waiting on the output, a non-streamed call is simpler and removes the partial-state risk entirely.
