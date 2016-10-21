# Part system

## Basics

The part system in Sanity is what allows plugins to add, extend or replace functionality in Sanity.

In essence; a part is a mapping from a string (ex: `part:@sanity/base/buttons/default`) to a file `/some/path/to/Button.js`.

A part is *declared* once, and can be *implemented* multiple times. For instance, the part above is *declared* in the `@sanity/base` plugin, and *implemented* in `@sanity/components`.

To *consume* the part in a different plugin, you use `require` or `import` statements as you normally would in a project utilizing Webpack or Browserify. The only difference is that instead of requiring a file by path, you require a part name.

Example:

```js
import React from 'react'
import Button from 'part:@sanity/base/buttons/default'

class ClickCounter extends React.Component {
  constructor() {
    super()

    this.state = {clicks: 0}
    this.handleClick = this.handleClick.bind(this)
  }

  handleClick() {
    this.setState({clicks: clicks++})
  }

  render() {
    return (
      <div>
        Number of clicks: {this.state.clicks}
        <Button onClick={this.handleClick}>Click me!</Button>
      </div>
    )
  }
}

export default ClickCounter
```

The reason why we're using and advocating this part system is to not lock the implementation to a specific plugin. With the above example, the plugin does not need to know which implementation of the button is actually being used, only that the imported button serves the same purpose.

For instance, a user could install the `sanity-plugin-beeping-button` plugin to have all buttons make a sound when clicked, or install the `sanity-plugin-bootstrap` to have a bootstrap layout applied to all core components.

For anyone writing custom plugins, the buttons would look and feel right, as long as they used the part name when consuming the button component.

## Order of precedence

If multiple plugins implement the same part, the last plugin listed in a projects `sanity.json` will end up as the authoritative part. This makes it possible to use for instance `@sanity/components` for most core components and overriding one or more part names with a more specific plugin.

## Loading all implementations

In some cases, you want to load all the implementations, not just the last to implement it. This can be done by prefixing the part name with the `all:` keyword. This pattern is common for things like registering actions in a toolbar, or when creating navigation between different tools.

Example:
```js
import targets from 'all:part:@sanity/base/share'

export default function ShareList(props) {
  return (
    <ul>
      {targets.map(target =>
        <li key={target.name}>
          <a href={target.url(props.targetUrl)}>{target.name}</a>
        </li>
      )}
    </ul>
  )
}
```

## Checking for an implementation

Sometimes you can't know if a part has been implemented in the current Sanity installation. For instance, you could define a part that will be called whenever navigation occurs, but it's not something that is critical to the flow of the application.

In cases like these, you can use the question mark as a postfix:
```js
import onNavigate from 'part:@sanity/base/router/onNavigate?'

if (onNavigate) {
  onNavigate(destinationUrl)
}
```

Note: You could in theory use the `all:` prefix as mentioned above - however this would cause every implementer to be a part of the generated javascript bundle, even if you are justing using a single implementer.

## Defining partss

Parts are defined in a plugins `sanity.json` file, under the `parts` key.

The two required properties are `name` and `description`:
```json
{
  "parts": [
    {
      "name": "part:bike-rental/bike-preview",
      "description": "React component that renders a preview of a given bike"
    }
  ]
}
```

## Implementing parts

Parts are implemented in a plugins `sanity.json` file, under the `parts` key.
The two required properties are `implements` and `path`:

```json
{
  "paths": {
    "source": "./src",
    "compiled": "./lib"
  },

  "parts": [
    {
      "implements": "part:bike-rental/bike-preview",
      "path": "components/BikePreview.js",
    },
    {
      "name": "style:bike-rental/bike-preview-default",
      "implements": "style:bike-rental/bike-preview",
      "path": "./styles/BikePreview.css"
    }
  ]
}
```

In the example above there are a few notable things:

* The plugin defines paths to both a `source` and a `compiled` directory. When these are defined, paths defined in part declarations that do not start with a `.` are relative to these paths based on which context the plugin is running in.

* The first implementation has a `path` property that is relative to the paths defined. When the plugin lives inside the `plugins` folder, code is automatically run through Babel, which means it'll run ES6 code, and it will look for the file in `<plugin-location>/src/components/BikePreview.js`. If you choose to publish a plugin to NPM, however, the code needs to be compiled to browser-compatible ES5 code. When a plugin is installed into `node_modules`, it will look for the compiled file in `<plugin-location>/lib/components/BikePreview.js`. If you do not require any Babel compilation, you can simply skip the declaration of `paths`.

* An implementation can also contain a `name`. This gives the actual *implementation* a fixed name, that cannot be implemented by other plugins. In the example above, we gave the style part a name. This allows other plugins to *compose from* the original implementation, extending it as need be.

* The style part declaration uses a relative path (`./styles/BikePreview.css`). The leading dot-notation will make Sanity look for the file relative to `sanity.json`, instead of using the `source` and `compiled` paths defined.
