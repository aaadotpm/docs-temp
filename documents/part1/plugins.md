# Plugins

Everything in Sanity is a plugin.

## To get plugins in Sanity working, you need

* [Node.js](nodejs.org) (version 4 or above)
* [NPM](npmjs.org)
* Basic JavaScript and terminal skills
* Have your own Sanity installation (see [getting started](getting-started.md))


## Adding a plugin

You have a choice between two options:

**Option 1: Install existing**

Install a pre-existing plugin into the `node_modules` folder. This is the *consumer* option. You download and use a plugin which is maintained somewhere else.

**Option 2: Create new**

Create a new plugin directly in the `plugins` folder. This is the *developer* option when creating a plugin for your Sanity installation. You can extract it later, if you want to make it available to others.


### A closer look at the options

Say you're running a bike rental shop. You want to use Sanity for managing your data, and you need a plugin called `bike-inventory`.

#### Option 1

The `bike-inventory` plugin already exists, and you want to install it. Run the Sanity plugin install command: `sanity install bike-inventory`

Assuming there is an NPM package named `sanity-plugin-bike-inventory` available, a few things will happen:

- The plugin will be installed into the `node_modules` folder.
- The plugin name will be appended to the `plugins` section of your root `sanity.json` file.
- If the plugin includes a `config.json.dist` file, two more things will happen:
  - The `config.json.dist` file will be copied to `configs/bike-inventory.json`
  - A checksum based on the `config.json.dist` file will be appended to `configs/.checksums`. This provides a fast way of checking if the installed plugin is up to date.

#### Option 2

You're a developer and want to create the `bike-inventory` plugin.

1. Create a folder for your plugin `mkdir plugins/bike-inventory && cd plugins/bike-inventory`
2. Run the Sanity plugin bootstrap command: `sanity init plugin`. Follow the prompts along the way.
3. Add your plugin name to `plugins` section of your root `sanity.json`. Usually you want to place the plugin at the bottom, eg:
```json
  {
    "plugins": [
      "@sanity/base",
      "@sanity/default-layout",
      "bike-inventory"
    ]
  }
```
4. Start writing your plugin! Check out [this sample plugin](example.com) if you're unsure on how to get started.


### Technical difference between the two options

As mentioned above, a plugin can reside in one of two locations:
- In the `node_modules` folder.
- In the `plugins` folder.

Sanity treats plugins differently, based on their location.

#### Plugins within `node_modules`
  - Need to be prefixed with `sanity-plugin-` to be recognized (optionally with a scope; `@yourcompany/<plugin-name>`).
  - Need to be runnable in a browser (ES5-compatible). ES6, JSX and similar are usually compiled as part of a pre-publish step for each plugin.

#### Plugins within `plugins`
  - Do *not* need to be prefixed with `sanity-plugin-` to be recognized.
  - Are automatically run through Babel if the `paths` property is set and has a `compiled` location. See the [role system](role-system.md) documentation for more information.

In general, you can think of plugins installed to `node_modules` as plugins you __consume__, while plugins inside of the `plugins` directory contain functionality local to your Sanity instance.


## List of available plugins

coming soon
