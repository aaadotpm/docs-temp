# Localization

Localization in Sanity is built on [react-intl](https://github.com/yahoo/react-intl), which in turn is built on the [ECMAScript Internationalization API](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Intl).

## To get localization in Sanity working, you need

* [Node.js](nodejs.org) (version 4 or above)
* [NPM](npmjs.org)
* Basic JavaScript and terminal skills

## TLDR;

1. Declare which languages you want to support.
2. Provide a key/value translation file for each of these languages.
3. Envelope your plugin in `SanityIntlProvider` and use any react-intl components normally.

## Setup in more detail

In your global `sanity.json` file, specify which language(s) your Sanity installation will support. E.g.:

```json
"locale": {
  "supportedLanguages": ["en-US", "nb-NO"]
}
```

Say you have a plugin called `bike-inventory`. In the plugin config `./plugins/bike-inventory/sanity.json`, append the following:

```json
{
  "parts": [
    {
      "implements": "part:@sanity/base/locale-messages",
      "path": "./locale/nb-NO.json"
    },
    {
      "implements": "part:@sanity/base/locale-messages",
      "path": "./locale/en-US.json"
    }
  ]
}
```

The above tells Sanity where your locale-messages files can be found. Make sure they exist...

```
touch plugins/bike-inventory/locale/en-US.json
touch plugins/bike-inventory/locale/nb-NO.json
```

...and that they include something useful, for instance:

```json
{
  "en-US": {
    "bike-inventory.welcome": "Welcome to Bike Inventory"
  }
}
```
```json
{
  "nb-NO": {
    "bike-inventory.welcome": "Velkommen til Sykkeloversikten"
  }
}
```

In your plugin, when you want to use these strings, you can rely on `FormattedMessage`, `FormattedNumber` and all the other components that is part of [react-intl](https://github.com/yahoo/react-intl). The only difference is that you want to import these components from the role `part:@sanity/base/locale/intl` instead of `react-intl`. In other words:

```js
import {FormattedMessage} from 'part:@sanity/base/locale/intl'

render() {
  return (
    <h1>
      <FormattedMessage id="bike-inventory.welcome" />
    </h1>
  )
}
```

This relies on the fact that the layout used has been wrapped in a `SanityIntlProvider`. While most users won't have to think about this - if you're writing a custom layout, you should make sure you're wrapping your children in this component. See [@sanity/default-layout](https://github.com/sanity-io/default-layout) for an example of how it's done. Basically:

```js
import SanityIntlProvider from 'part:@sanity/base/sanity-intl-provider'
import {FormattedMessage} from 'part:@sanity/base/locale/intl'
import config from 'config:sanity'

export default function myLayout() {
  return (
    <SanityIntlProvider supportedLanguages={config.locale.supportedLanguages}>
      <h1>
        <FormattedMessage id="my-layout.header" />
      </h1>
    </SanityIntlProvider>
  )
}
```
