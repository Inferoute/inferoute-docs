# Supported parameters

Inferoute accepts an OpenAI-shaped request and forwards the parameters both vLLM and llama.cpp can run. A key that is not on the list for that endpoint returns **400**. A parameter that is on the list but not enabled on the provider that can serve the model returns **400** `capability_unavailable` once every provider has been checked, or **503** while checks are still running.

## Chat and completions

You can send `model`, `messages` or `prompt`, `stream`, `max_tokens`, `max_completion_tokens`, `temperature`, `top_p`, `top_k`, `min_p`, `stop`, `seed`, `frequency_penalty`, `presence_penalty`, `repetition_penalty`, `tools`, `tool_choice`, `response_format`, `logprobs`, `top_logprobs`, and `stream_options`.

`n` and `best_of` may be `1`. Any larger value returns **400**. Inferoute bills one completion.

`inferoute`, `user`, `metadata`, `store`, and `service_tier` are accepted and not forwarded to the engine.

## Embeddings

Send `model` and `input`. `encoding_format` may be `float` or `base64`. `user` is accepted and not forwarded. `dimensions` and generation parameters return **400**.

A batch may contain at most **256** inputs. An empty string is rejected. Each input must fit the model's context length. The batch must stay within **300,000** tokens.

## Time limits

A non-streaming request has **5 minutes** for the provider to finish. If that deadline hits while you are still connected, the response is **504** with code `inference_deadline_exceeded` and message `provider did not finish within the non-streaming time limit`.

A streaming request has **2 minutes** to produce the first generated output, then **1 minute** between later generated chunks, and **30 minutes** in total.

If nothing was delivered, you are not billed. After the first generated output is delivered, you pay for the input once and for the output that was delivered.

## Output length

If you omit both token limits, the platform cap is **16,384** generated tokens. If you set either limit, Inferoute uses the smaller of the values you set, and never more than 16,384. Only `max_tokens` is forwarded.
