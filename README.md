# React Turnstile

[![npm version][npm-version-src]][npm-version-href]
[![npm downloads][npm-downloads-src]][npm-downloads-href]
[![package size][packagephobia-src]][packagephobia-href]
![Tests Missing][tests-src]
![PRs Welcome][prs-src]

> [Cloudflare Turnstile](https://developers.cloudflare.com/turnstile/) integration for React.

## Features

- 💪 smart verification with minimal user interaction
- 🕵️‍♀️ privacy-focused approach
- ⚡️ lightweight - script only loaded when required

## Demo

https://react-turnstile.vercel.app/

## Install
1. First, [follow these steps](https://developers.cloudflare.com/turnstile/get-started/) to obtain a free site key and secret key from Cloudflare.
2. Install `react.turnstile` into your React application.

	```bash
	# Whichever matches your package manager
	pnpm add react.turnstile
	npm install react.turnstile
	yarn add react.turnstile
	```

## Usage

The only required prop is the `siteKey`.

```jsx
import { Turnstile } from 'react.turnstile'

function TurnstileWidget() {
  return (
    <Turnstile siteKey='1x00000000000000000000AA' />
  )
}
```

## Props

| **Prop**    | **Type**   | **Description**                                                                                                                                                                                                                                                 | **Required** |
| ----------- | ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------ |
| siteKey     | `string`   | Your sitekey key, get one from [here](https://developers.cloudflare.com/turnstile/get-started/).                                                                                                                                                                | ✅            |
| options     | `object`   | Widget render options. More info about this options [below](https://github.com/marsidev/react.turnstile/#render-options).                                                                                                                                       |              |
| scriptProps | `object`   | You can customize the injected `script` tag with this prop. It allows you to add `async`, `defer`, `nonce` attributes to the script tag. You can also control whether the injected script will be added to the document body or head with `appendTo` attribute. |              |
| onSuccess   | `function` | Callback that is invoked upon success of the challenge. The callback is passed a token that can be validated.                                                                                                                                                   |              |
| onExpire    | `function` | Callback that is invoked when a challenge expires.                                                                                                                                                                                                              |              |
| onError     | `function` | Callback that is invoked when there is a network error.                                                                                                                                                                                                         |              |


### Render options
| **Option** | **Type**                  | **Default**      | **Description**                                                                                                                                                                                                            |
| ---------- | ------------------------- | ---------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| container  | `string` or `HTMLElement` | `'cf-turnstile'` | Container ID or container node that will wrap the widget iframe.                                                                                                                                                           |
| theme      | `string`                  | `'auto'`         | The widget theme. You can choose between `light`, `dark` or `auto`.                                                                                                                                                        |
| tabIndex   | `number`                  | `0`              | The `tabindex` of Turnstile’s iframe for accessibility purposes.                                                                                                                                                           |
| action     | `string`                  | `undefined`      | A customer value that can be used to differentiate widgets under the same `sitekey` in analytics and which is returned upon validation. This can only contain up to 32 alphanumeric characters including `_` and `-`.      |
| cData      | `string`                  | `undefined`      | A customer payload that can be used to attach customer data to the challenge throughout its issuance and which is returned upon validation. This can only contain up to 255 alphanumeric characters including `_` and `-`. |

> Read [the docs](https://developers.cloudflare.com/turnstile/get-started/client-side-rendering/#configurations) to get more info about this options.

> The widget is wrapped in a `div`, so you can pass any valid `div` prop such as `className` or `style`.

### Script options

| **Option**         | **Type**  | **Default**                 | **Description**                                         |
| ------------------ | --------- | --------------------------- | ------------------------------------------------------- |
| nonce              | `string`  | `undefined`                 | Custom nonce for the injected script.                   |
| defer              | `boolean` | `true`                      | Define if set the injected script as defer.             |
| async              | `boolean` | `true`                      | Define if set the injected script as async.             |
| appendTo           | `string`  | `'head'`                    | Define if inject the script in the head or in the body. |
| id                 | `string`  | `'cf-turnstile-script'`     | Custom ID of the injected script.                       |
| onLoadCallbackName | `string`  | `'onloadTurnstileCallback'` | Custom name of the onload callback.                     |

## Examples

### Rendering the widget:
```jsx
import { Turnstile } from 'react.turnstile'

function TurnstileWidget() {
  return <Turnstile siteKey='1x00000000000000000000AA' />
}
```

### Rendering the widget with custom props:
```jsx
import { Turnstile } from 'react.turnstile'

function TurnstileWidget() {
  return (
    <Turnstile
      siteKey='1x00000000000000000000AA'
      className='fixed bottom-4 right-4'
      options={{
        action: 'submit-form',
        theme: 'light'
      }}
      scriptOptions={{
        appendTo: 'body'
      }}
    />
  )
}
```

### Managing widget rendering status:
```jsx
import { useState } from 'react'
import { Turnstile } from 'react.turnstile'

function TurnstileWidget() {
  const [status, setStatus] = useState()

  return (
    <Turnstile
      siteKey='1x00000000000000000000AA'
      onError={() => setStatus('error')}
      onExpire={() => setStatus('expired')}
      onSuccess={() => setStatus('solved')}
    />
  )
}
```

### Getting the token after solving the challenge:
```jsx
import { useState } from 'react'
import { Turnstile } from 'react.turnstile'

function TurnstileWidget() {
  const [token, setToken] = useState()

  return (
    <Turnstile
      siteKey='1x00000000000000000000AA'
      onSuccess={(token) => setToken(token)}
    />
  )
}
```

### Interacting with the widget:
```jsx
import { useRef } from 'react'
import { Turnstile } from 'react.turnstile'

function TurnstileWidget() {
  const ref = useRef(null)

  return (
    <>
      <Turnstile ref={ref} siteKey='1x00000000000000000000AA'/>

      <button onClick={() => alert(ref.current?.getResponse())}>
        Get response
      </button>

      <button onClick={() => ref.current?.reset()}>
        Reset widget
      </button>

      <button onClick={() => ref.current?.remove()}>
        Remove widget
      </button>

      <button onClick={() => ref.current?.render()}>
        Render widget
      </button>
    </>
  )
}
```

### Interacting with the widget (using TypeScript):
```jsx
import { useRef } from 'react'
import { Turnstile, type TurnstileInstance } from 'react.turnstile'

function TurnstileWidget() {
  const ref = useRef<TurnstileInstance>(null)

  return (
    <>
      <Turnstile ref={ref} siteKey='1x00000000000000000000AA'/>

      <button onClick={() => alert(ref.current?.getResponse())}>
        Get response
      </button>
    </>
  )
}
```

### Validating a token:
```jsx
// TurnstileWidget.jsx
import { useRef, useState } from 'react'
import { Turnstile } from 'react.turnstile'

function TurnstileWidget() {
  const formRef = useRef(null)

  async function handleSubmit(event) {
    event.preventDefault()
    const formData = new FormData(formRef.current)
    const token = formData.get('cf-turnstile-response')

    const res = await fetch('/api/verify', {
      method: 'POST',
      body: JSON.stringify({ token }),
      headers: {
        'content-type': 'application/json'
      }
    })

    const data = await res.json()
    if (data.success) {
      // the token has been validated
    }
  }

  return (
    <form ref={formRef} onSubmit={handleSubmit}>
      <input type="text" placeholder="username"/>
      <input type="password" placeholder="password"/>
      <Turnstile siteKey='1x00000000000000000000AA'/>
      <button type='submit'>Login</button>
    </form>
  )
}
```

```js
// api/verify.js
// this code runs on the server - this is an example of a next.js api route
const endpoint = 'https://challenges.cloudflare.com/turnstile/v0/siteverify'
const secret = '1x0000000000000000000000000000000AA'

export default async function handler(request, response) {
  const body = `secret=${encodeURIComponent(secret)}&response=${encodeURIComponent(request.body.token)}`

  const res = await fetch(endpoint, {
    method: 'POST',
    body,
    headers: {
      'content-type': 'application/x-www-form-urlencoded'
    }
  })

  const data = await res.json()
  return response.json(data)
}
```

> Check the [demo](https://react-turnstile.vercel.app/) and his [source code](/packages/example/src/pages/index.tsx) to see a code similar to the above in action.

> Check [the docs](https://developers.cloudflare.com/turnstile/get-started/server-side-validation/) for more info about server side validation.

> As you might noted, there is three ways to get the token response from a solved challenge:
> - by catching it from the `onSuccess` callback.
> - by calling the `.getResponse()` method.
> - by reading the widget response input with name `cf-turnstile-response`.


## Credits

Inspired by
- [nuxt-turnstile](https://github.com/danielroe/nuxt-turnstile)
- [svelte-turnstile](https://github.com/ghostdevv/svelte-turnstile)
- [reaptcha](https://github.com/sarneeh/reaptcha)


## License

Published under the [MIT License](./LICENCE).


<!-- Badges -->

[npm-version-src]: https://img.shields.io/npm/v/react.turnstile?style=flat-square
[npm-version-href]: https://npmjs.com/package/react.turnstile
[npm-downloads-src]: https://img.shields.io/npm/dm/react.turnstile?style=flat-square
[npm-downloads-href]: https://npmjs.com/package/react.turnstile
<!-- [github-actions-src]: https://img.shields.io/github/workflow/status/danielroe/nuxt-turnstile/ci/main?style=flat-square -->
<!-- [github-actions-href]: https://github.com/danielroe/nuxt-turnstile/actions?query=workflow%3Aci -->
<!-- [codecov-src]: https://img.shields.io/codecov/c/gh/danielroe/nuxt-turnstile/main?style=flat-square -->
<!-- [codecov-href]: https://codecov.io/gh/danielroe/nuxt-turnstile -->
[packagephobia-href]: https://packagephobia.com/result?p=react.turnstile
[packagephobia-src]: https://packagephobia.com/badge?p=react.turnstile
[prs-src]: https://img.shields.io/badge/PRs-welcome-brightgreen.svg
[tests-src]: https://img.shields.io/badge/Tests-missing-red.svg