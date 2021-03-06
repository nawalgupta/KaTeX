---
id: autorender
title: Auto-render Extension
---
This is an extension to automatically render all of the math inside of text. It
searches all of the text nodes within a given element for the given delimiters,
ignoring certain tags like `<pre>`, and renders the math in place.

## Usage
This extension isn't part of KaTeX proper, so the script needs to be included
(via a `<script>` tag) in the page along with KaTeX itself.  For example,
using a CDN:

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.10.0-beta/dist/katex.min.css" integrity="sha384-9tPv11A+glH/on/wEu99NVwDPwkMQESOocs/ZGXPoIiLE8MU/qkqUcZ3zzL+6DuH" crossorigin="anonymous">
<script src="https://cdn.jsdelivr.net/npm/katex@0.10.0-beta/dist/katex.min.js" integrity="sha384-U8Vrjwb8fuHMt6ewaCy8uqeUXv4oitYACKdB0VziCerzt011iQ/0TqlSlv8MReCm" crossorigin="anonymous"></script>
<script src="https://cdn.jsdelivr.net/npm/katex@0.10.0-beta/dist/contrib/auto-render.min.js" integrity="sha384-aGfk5kvhIq5x1x5YdvCp4upKZYnA8ckafviDpmWEKp4afOZEqOli7gqSnh8I6enH" crossorigin="anonymous"></script>
```

Then, call the exposed `renderMathInElement` function in a script tag
before the close body tag:

```html
<body>
  ...
  <script>
    renderMathInElement(document.body);
  </script>
</body>
```

If you prefer to have all your setup inside the html `<head>`,
you can use the following script there
(instead of the one above at the end of the `<body>`):

```html
<head>
  ...
  <script>
    document.addEventListener("DOMContentLoaded", function() {
      renderMathInElement(document.body);
    });
  </script>
  ...
</head>
```

## API
This extension exposes a single function, `window.renderMathInElement`, with
the following API:

```js
function renderMathInElement(elem, options)
```

`elem` is an HTML DOM element. The function will recursively search for text
nodes inside this element and render the math in them.

`options` is an optional object argument that can have the same keys as [the
object passed to `katex.render`](https://github.com/Khan/KaTeX/#rendering-options),
in addition to two auto-render-specific keys:

- `delimiters`: This is a list of delimiters to look for math. Each delimiter
  has three properties:

    - `left`: A string which starts the math expression (i.e. the left delimiter).
    - `right`: A string which ends the math expression (i.e. the right delimiter).
    - `display`: A boolean of whether the math in the expression should be
      rendered in display mode or not.

  The default value is:

  ```js
  [
    {left: "$$", right: "$$", display: true},
    {left: "\\(", right: "\\)", display: false},
    {left: "\\[", right: "\\]", display: true}
  ]
  ```

- `ignoredTags`: This is a list of DOM node types to ignore when recursing
  through. The default value is
  `["script", "noscript", "style", "textarea", "pre", "code"]`.

- `errorCallback`: A callback method returning a message and an error stack
  in case of an critical error during rendering. The default uses `console.error`.

The `displayMode` property of the options object is ignored, and is
instead taken from the `display` key of the corresponding entry in the
`delimiters` key.

The same `options.macros` object (which defaults to an empty object `{}`)
is passed into several calls to `katex.render`, so that consecutive equations
can build up shared macros by `\gdef`.
