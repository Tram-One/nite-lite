# Tram-Deco

_Declarative Custom Elements using native Web Component APIs and specs._

Tram-Deco provides a more elegant interface for building Web Components, that remains as close as possible to the
existing browser APIs. Tram-Deco is an experiment to determine the value of a declarative interface for building Web
Components, without the addition of APIs that don't already exist.

<img src="https://img.shields.io/npm/dm/tram-deco.svg" alt="Downloads">
<img src="https://img.shields.io/npm/v/tram-deco.svg" alt="Version">
<a href="https://unpkg.com/tram-deco@5/tram-deco.min.js"><img src="https://img.shields.io/badge/gzip-960B-006369.svg?style=flat" alt="Gzipped Size"></a>
<a href="https://github.com/Tram-One/tram-deco/blob/main/LICENSE"><img src="https://img.shields.io/npm/l/tram-deco.svg" alt="License"></a>
<a href="https://discord.gg/dpBXAQC"><img src="https://img.shields.io/badge/discord-join-5865F2.svg?style=flat" alt="Join Discord"></a>
<a href="https://codepen.io/pen?template=JjzQmaL"><img src="https://img.shields.io/badge/codepen-template-DD6369.svg?style=flat" alt="Codepen Template"></a>

## Example

```html
<!-- include the Tram-Deco library -->
<script src="https://unpkg.com/tram-deco@5"></script>
<script>
  TramDeco.watch();
</script>

<!-- define some web components -->
<template td-definitions>
  <!-- definition for a custom-title tag! -->
  <custom-title>
    <!-- declarative shadow dom for the insides -->
    <template shadowrootmode="open">
      <!-- styles, for just this element -->
      <style>
        h1 {
          color: blue;
        }
      </style>

      <!-- dom, to show on the page -->
      <h1>
        <slot>Hello World</slot>
      </h1>
      <hr />
    </template>

    <!-- scripts, that let you define lifecycle methods -->
    <script td-method="connectedCallback">
      this.shadowRoot.querySelector('slot').addEventListener('slotchange', () => {
        document.title = this.textContent || 'Hello World';
      });
    </script>
  </custom-title>
</template>

<!-- use our new element! -->
<custom-title>Tram-Deco is Cool!</custom-title>
```

[Live on Codepen](https://codepen.io/JRJurman/pen/XWQeqaX)

## How to use

> [!important]
>
> Tram-Deco depends on declarative shadow DOM, which at the time of writing is not available on all browsers. Check
> [caniuse.com](https://caniuse.com/declarative-shadow-dom) to understand browser support and coverage here.

The most straight-forward way to use Tram-Deco is to include the script in your project, and call `TramDeco.watch()`.
There are other ways to build components listed in the JS API section below, but this will automatically find and build
component definitions in your project.

```html
<script src="https://unpkg.com/tram-deco@5"></script>
<script>
  TramDeco.watch();
</script>
```

If you want the minified version you can point to that instead:

```html
<script src="https://unpkg.com/tram-deco@5/tram-deco.min.js"></script>
```

## API

### JS API

Tram-Deco exposes several different API methods that you can call to build Web Components, depending on your use case.

<dl>
<dt><code>TramDeco.watch()</code></dt>
<dd>

The most straight-forward way to build Web Component definitions. The `watch()` function starts a mutation observer that
watches for template tags with the `td-definitions` attribute. When these appear in the DOM, Tram-Deco will process
them, and build the component definitions inside. When it finishes processing these, it updates the template with an
attribute `defined`.

</dd>
<dt><code>TramDeco.define(elementDefinition)</code></dt>
<dd>

The `define` function takes in a single tag with a declarative shadow dom template, and turns it into a Web Component
definition. In the above example, it is everything inside the `td-definitions` template. This is useful if you have a
single component you would like to define, potentially with a specific version of Tram-Deco.

</dd>
<dt><code>TramDeco.import(componentPath)</code></dt>
<dd>

> [!warning]
>
> Importing by path requires `Document.parseHTMLUnsafe` which is only available (at the time of writing) on technical
> previews of most browsers.

The `import` function takes in a path to a component definition file, and defines all component definitions inside as
new Web Component definitions. In the above example, it is everything inside the `td-definitions` template. This is
useful if you want to save your component definitions in separate files.

</dd>
</dl>

### HTML API

Tram-Deco exposes the following attributes to help you build and configure declarative web components

#### Top Level API

<dl>
<dt><code>td-definitions</code></dt>
<dd>

Attribute to be used on the `<template>` surrounding your component definitions. You can have multiple templates, or
just a single one for all of your definitions. These template tags will be picked up automatically with
`TramDeco.watch()`.

</dd>
</dl>

#### Component API

These attributes can be used to provide logic for different life cycle events of your component. They follow the
standard API for Web Components.

<dl>
<dt><code>td-property="propertyName"</code></dt>
<dd>

Attribute to be used on a `script` tag in your component definition. This assigned property name will be attached to the
element as a static property, and can be useful for adding `observedAttributes`, `formAssociated`, `disableInternals`,
or `disableShadow`. You can also define custom static properties for your element.

</dd>
<dt><code>td-method="methodName"</code></dt>
<dd>

Attribute to be used on a `script` tag in your component definition. The assigned method name will be attached to the
element, and can be useful for adding to the `constructor`, or setting other Web Component APIs, such as
`connectedCallback`, `disconnectedCallback`, `adoptedCallback`, or `attributeChangedCallback`. You can also define
custom methods for your element.

</dd>
</dl>

#### Example Using Component API

```html
<script src="https://unpkg.com/tram-deco@5"></script>
<script>
  TramDeco.watch();
</script>

<template td-definitions>
  <my-counter>
    <!-- observed attributes, to watch for attribute changes on count -->
    <script td-property="observedAttributes">
      ['count'];
    </script>

    <template shadowrootmode="open" shadowrootdelegatesfocus>
      <button><slot>Counter</slot>: <span>0</span></button>
    </template>

    <!-- when we mount this component, add an event listener -->
    <script td-method="connectedCallback">
      const button = this.shadowRoot.querySelector('button');
      button.addEventListener('click', (event) => {
        const newCount = parseInt(this.getAttribute('count')) + 1;
        this.setAttribute('count', newCount);
      });
    </script>

    <!-- when the count updates, update the template -->
    <script td-method="attributeChangedCallback">
      const span = this.shadowRoot.querySelector('span');
      span.textContent = this.getAttribute('count');
    </script>
  </my-counter>
</template>

<my-counter count="0">Tram-Deco</my-counter>
```

[Live on Codepen](https://codepen.io/JRJurman/pen/GRLMdvd)

## contributions / discussions

If you think this is useful or interesting, I'd love to hear your thoughts! Feel free to
[reach out to me on mastodon](https://fosstodon.org/@jrjurman), or join the
[Tram-One discord](https://discord.gg/dpBXAQC).
