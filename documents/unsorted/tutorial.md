## TODO: make it so the CLI has a "install tutorial dataset" option

```
npm install -g @sanity/cli
mkdir /path/to/bike-rental && cd /path/to/bike-rental
sanity init
sanity start
open http://localhost:3910
```

## What did I just do?

Say you're running a bike rental and want to keep track of your data using Sanity.

1. Install the Sanity CLI tool globally - `npm install -g @sanity/cli`
2. Create a folder where you want your Sanity installation, named "bike-rental" in this example, to live - `mkdir /path/to/bike-rental && cd /path/to/bike-rental`
3. Run the Sanity bootstrap process - `sanity init`. Follow the prompts along the way.
4. Once dependencies are finished installing, run `sanity start`.
5. Point your browser at the URL provided in the terminal, for instance `http://localhost:3910`
