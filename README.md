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

## Project structure

```
.
├── index.html   # The whole app: markup, styles, and script
└── README.md
```

## Tech

- Plain HTML, CSS, and JavaScript, with no frameworks
- Fonts: [Schibsted Grotesk](https://fonts.google.com/specimen/Schibsted+Grotesk) and [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) from Google Fonts. Without a connection, the page falls back to system fonts.
- Responsive: the panes sit side by side on wide screens and stack on narrow ones
