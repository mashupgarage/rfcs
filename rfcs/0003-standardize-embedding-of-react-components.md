---
title: Standardize embedding of React components
submitted_by: Rico Sta. Cruz (@rstacruz)
submitted_on: 2019-03-13
accepted_on: null
---

## Summary

**Let's officially deprecate the use of `onmount`**, and migrate to using web components as a way to embed React components in webpages using [Remount]. This will allow us to:

[Remount]: https://github.com/rstacruz/remount
[Onmount]: https://github.com/rstacruz/onmount

- Use one library, React, as a standard way of writing JavaScript components.

- Write JS/Elixir "bridges" with less Elixir code.

## What we do now

Most MG projects use onmount to apply "behaviors" to existing DOM elements. As an example, we may have markup that has a `data-js` attribute, or a `role` attribute:

```html
<div
  data-js-user-profile
  data-user-name="rstacruz"
  data-email="rico@gmail.com"
/>

<!-- or -->
<div role='user-profile' ... />
```

We often use [Onmount] to attach RSJS-style behaviors to these nodes.

```js
onmount('[data-js-user-profile]', function () {
  // Get props as a JSON object via jQuery
  const props = $(this).data()

  // Pass them onto React
  ReactDOM.render(<UserProfile {...props} />, this)
})
```

## :warning: Problem: jQuery magic

Our solution now relies on strange jQuery behavior of [jQuery.fn.data](https://api.jquery.com/data/). This is weird because:

- It tries to magically convert kebab-case-keys into camelCaseKeys. For instance, `data-user-name` will be converted to `userName`. While this is desirable, it's a layer of abstraction we can do away with.

- It will cast types automatically. This is not the fault of JavaScript, but it's [behavior explicitly implemented in jQuery](https://stackoverflow.com/questions/26126017/html5-data-attribute-type-casting-strings-and-numbers). For instance:

  ```js
  <div data-version='1.2'>

  const ver = $('div').data('version')

  console.log(ver)
  // ⇒ 1.2

  console.log(typeof ver)
  // ⇒ Number
  ```

Solution: This can be remedied by using standard DOM behavior, eg, with `JSON.parse(div.getAttribute('data-props'))`.

## :warning: Problem: React memory leaks

Our solution does `ReactDOM.render()` when the elements appear, but it doesn't do `ReactDOM.unmountComponentAtNode()` when it disappears. While it's not a common case, it's something that can lead to unnecessary side effects and memory leaks.

The solution is to use onmount's _unmount_ handler, like so:

```js
onmount('[data-js-my-behavior]', function () {
  const props = JSON.parse(this.getAttribute('data-props'))
  ReactDOM.render(<MyComponent {...props} />, this)
}, function () {
  ReactDOM.unmountComponentAtNode(this)
})
```

...but this increases our boilerplate code quite significantly. It also poses the strange transition period of having some behaviors implemented _with_ the unmount handler, and some _without_, where we'd have inconsistent code.

## :rocket: Solution

**Use [Remount] to use React components as web components.** This means something like a `MyComponent` React component can be used in HTML as `<x-my-component>`.

<p align='center'><img src='https://raw.githubusercontent.com/rstacruz/remount/master/docs/images/remount.png' width='300'></p>

## Example implementation

You can write custom elements that we'll bind React components to later on. In this example, we may have a `<x-my-component>` custom element.

```html
<x-my-component props-json='{"userName":"rstacruz","email":"rico@gmail.com"}' />
```

You can even write this in Elixir very cleanly. Here's an example using Expug:

```pug
x-my-component(
  props-json=Jason.encode(%{
    "userName" => @user.name,
    "email" => @user.email
  })
)
```

In JavaScript, use Remount's `define()` to link `x-my-component` to a React component.

```js
// In this example, we're defining 2 React components to be used as web components.

import MyComponent from '@slerp-components/MyComponent'
import HelloWorld from '@slerp-components/Helloworld'
import { define } from 'remount'

define({
  'x-my-component': MyComponent,
  'x-hello-world': HelloWorld
})
```

## Refactoring Elixir / Expug

Here's an example directly lifted from Slerp. It uses onmount to attach a React element to `#fulfillment-location`.

```pug
#fulfillment-location(
  data-merchant-id= @merchant.id
  data-address= @cart.address
  data-storelist= Backroom.Shop.Collect.StoreView.store_json(@store_list)
  data-store= get_store_display_name(@store, @merchant)
  data-csrftoken="#{get_csrf_token()}"
  data-deliver-path="#{shop_merchant_deliver_address_lookup_path(@conn, :address_lookup, @merchant)}"
  data-image= Backroom.Shop.LayoutView.image_url_for(Persistence.Image, @merchant.setting.cover_image, @merchant.setting, :cover))
```

Using Remount's `props-json` support, we can now write the props using the more familiar Elixir map syntax. It can be written as:

```pug
x-fulfillment-location(
  props-json=Jason.encode(%{
    "data-merchant-id" => @merchant.id,
    "data-address" => @cart.address,
    "data-storelist" => ...
  })
)
```

This map can also be generated from a function. This has a great bonus of being very easily testable:

```sh
def fulfillment_location_props(merchant, cart) do
  %{
    "data-merchant-id" => merchant.id,
    "data-address" => cart.address,
    "data-storelist" => ...
  }
end
```

```pug
x-fulfillment-location(
  props-json=Jason.encode(fulfillment_location_props)
)
```

## Implementation plan

For projects that use Onmount:

 - Stop writing new `onmount()` behaviors. Write them using `define()` in Remount instead.

 - When refactoring `onmount()` behaviors, consider rewriting them as Remount components instead.

For new projects:

- Consider using Remount in new projects from the start, and don't add Onmount.

## References

- [Onmount][onmount] _(github.com)_
- [Remount][remount] _(github.com)_

---

# FAQ

## Can you do behaviors with Remount?

No, Remount is not used for behaviors. It's used to _mount_ a React component to a part of the DOM tree. On the other hand, a _behavior_ is more like a "decorator" than enhances a DOM element with additional event handlers and such.

Remount lets you write custom elements (aka, web components) using React. In behaviors, a given DOM element/fragment will only be _enhanced_ by an onmount behavior, where Remount makes it so that the entire DOM element/fragment is managed by a React component completely.

For example, here is a `data-js-track-click` behavior:

```html
<a href='https://google.com' data-js-track-click>
  Go to <em>Google.com</em>
</a>
```

There (presumably) is a _data-js-track-click_ behavior that adds an `onclick` handler to the link that the attribute is attached to.

This cannot be done in Remount. Instead, it might be done this way using Remount custom elements:

```html
<x-google-link />
```

...where the entire link HTML is rendered using React, and the click-tracking will be done from within React, and so on.
