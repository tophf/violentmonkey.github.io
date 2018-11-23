---
title: Inject scripts into different contexts
date: 2018-11-23 23:35:11
category: feature
tags:
  - inject
path: "/2018/11/23/inject-into-context/"
---

Since Violentmonkey v2.10.0, a new type of metadata named [`@inject-into`](/api/metadata-block/#inject-into) is introduced. With its help, **scripts can be injected into CSP restricted pages in Firefox now!**

## Contexts

As we know, there are two types of context for a script to execute in:

- context of a web page

    The context in which all scripts of a web page execute. We will call it "*page context*".

- context of content scripts

    The context in which [content scripts](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/Content_scripts) execute. We will call it "*content context*" later.

## Injection Modes

In earlier versions of Violentmonkey, all scripts will be injected into the *page context*.

This works well at most times in Chrome. However, Firefox works differently. All scripts will be blocked in websites with CSP rules blocking inline scripts, like [GitHub](https://github.com). See issue [#173](https://github.com/violentmonkey/violentmonkey/issues/173), [#408](https://github.com/violentmonkey/violentmonkey/issues/408).

By introducing `@inject-into`, userscripts can be injected in Firefox now. In pages with CSP restrictions, we can inject userscripts into the *content context*.

### Mode: `page`

This is the default mode, as the behavior in Violentmonkey v2.9.x.

`unsafeWindow` refers to `window` object of the web page.

### Mode: `content`

```js
// ==UserScript==
// @inject-into content
// ==/UserScript==
```

In this mode, scripts will be injected into the *content context*.

The *content context* is an isolated world, scripts in this context have no access to JavaScript objects from *page context*. But they can communicate with DOM APIs such as `addEventListener`.

`unsafeWindow` refers to the `global` object of *content context*.

**Scripts requiring access to JavaScript objects in the web page will not work in this mode.** For example, `unsafeWindow.jQuery` becomes inaccessible even if jQuery is introduced in the page.

### Mode: `auto`

```js
// ==UserScript==
// @inject-into auto
// ==/UserScript==
```

In this mode, scripts will be injected into the *page context* if possible. If blocked by CSP, they will be injected into the *content context*. It is the script author's job to check the environments.

## Violentmonkey Settings

By default, all scripts will be injected into the *page context*. This can be changed in the settings tab of Violentmonkey.