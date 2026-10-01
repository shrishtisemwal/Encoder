# Encoder

A single-page Base64 encoder and decoder for JSON API payloads. It runs in the browser. There is no build step, there are no dependencies, and nothing you type leaves the page.

## Features

- **Encode and decode side by side.** Results update as you type.
- **JSON-aware.** With **Is Object** ticked, the encoder checks and minifies the JSON before encoding. The decoder pretty-prints the result.
- **Plain text mode.** Untick **Is Object** to encode or decode any text without JSON parsing.
- **Forgiving decoder.** It accepts URL-safe Base64 (`-` and `_`), missing `=` padding, and stray whitespace or line breaks. It also handles plain Base64 of UTF-8 text that was not URI-encoded first.
- **Round-trip buttons.** **Decode this →** and **← Encode this** send the output to the other pane, so you can check that a value round-trips.
- **Clear errors.** Invalid JSON, invalid Base64, or a result that isn't JSON each show a message under the output.
- **One-click copy** for each output, plus **Clear** buttons per pane and **Clear both**.

## How it works

Encoding:

```
input → JSON.stringify (if Is Object) → encodeURI → btoa (Base64)
```

Decoding:

```
input → atob (Base64) → decodeURI → JSON.parse + pretty-print (if Is Object)
```

Running `encodeURI` before Base64 keeps non-ASCII characters (accents, emoji, other scripts) safe, because `btoa` only accepts Latin-1 characters.

### Example

| Step | Value |
| --- | --- |
| Input | `{"employee_id": 1042}` |
| After `JSON.stringify` | `{"employee_id":1042}` |
| After `encodeURI` | `%7B%22employee_id%22:1042%7D` |
| Base64 output | `JTdCJTIyZW1wbG95ZWVfaWQlMjI6MTA0MiU3RA==` |

## Usage

Open `index.html` in any modern browser:

```sh
open index.html          # macOS
xdg-open index.html      # Linux
start index.html         # Windows
```

To serve it locally instead:

```sh
python3 -m http.server 8000
# then visit http://localhost:8000
```

> The **Copy** button uses the Clipboard API. Some browsers block it on `file://` pages. If that happens, the output text is selected for you, so press ⌘C / Ctrl+C to copy it.

## Things to know

- **Not plain Base64.** The output is Base64 of the *URI-encoded* text, so a standard Base64 decoder (such as `base64 -d`) returns something like `%7B%22employee_id%22:1042%7D`, not the original JSON. Use this tool's decoder, or run `decodeURIComponent` on the result yourself.
- **Standard alphabet out.** The encoder outputs standard Base64 with `+`, `/` and `=` padding. If you need URL-safe output, replace `+` with `-` and `/` with `_`. The decoder accepts both forms.
- **Whitespace.** The encoder trims leading and trailing whitespace. With **Is Object** ticked, the JSON is minified, so formatting inside it is not kept either.
- **Key order and numbers** are kept as `JSON.parse` reads them. Very large integers (beyond 2^53) lose precision, as they do in any JavaScript JSON parser.
- **Is Object syncs one way.** **← Encode this** copies the decoder's **Is Object** setting to the encoder. **Decode this →** leaves the decoder's setting alone.

## Troubleshooting

| Message | What it means | What to do |
| --- | --- | --- |
| *Input is not valid JSON* | **Is Object** is ticked but the input doesn't parse as JSON. | Fix the JSON (watch for trailing commas and single quotes), or untick **Is Object**. |
| *Input is not valid Base64* | The decoder input has characters outside the Base64 alphabet, or the wrong length. | Check that the whole value was pasted, with no cut-off characters or extra quotes. |
| *Decoded, but the result is not JSON* | The Base64 was valid but the decoded text isn't JSON. | Untick **Is Object** in the decoder if you expect plain text. |
| *Could not encode this text* | The input has characters `encodeURI` can't handle, such as a broken emoji (a lone surrogate). | Retype or remove the damaged character. |

## Privacy

All encoding and decoding happens in your browser with built-in JavaScript functions. Your input is never sent to a server or saved. The only network requests are for the Google Fonts stylesheet and font files.

## Browser support

Works in current versions of Chrome, Edge, Firefox and Safari. It needs `TextDecoder`, `navigator.clipboard`, and CSS grid, which every modern browser has.

## Contributing

1. Fork the repo and create a branch.
2. Edit `index.html`. Styles are in the `<style>` block and logic is in the `<script>` block at the bottom.
3. Open the page in a browser and check that a few values round-trip: JSON, plain text, and text with non-ASCII characters such as `café` or `日本`.
4. Open a pull request that describes the change.

## Project structure

```
.
├── index.html   # The whole app: markup, styles, and script
├── favicon.svg  # Browser tab icon
└── README.md
```

## Tech

- Plain HTML, CSS, and JavaScript, with no frameworks
- Fonts: [Schibsted Grotesk](https://fonts.google.com/specimen/Schibsted+Grotesk) and [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) from Google Fonts. Without a connection, the page falls back to system fonts.
- Responsive: the panes sit side by side on wide screens and stack on narrow ones
