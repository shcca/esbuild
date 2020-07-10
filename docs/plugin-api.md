# Plugin API

## Example plugins

### YAML loader

```js
let yamlLoader = plugin => {
  plugin.setName('yaml-loader')
  plugin.addLoader({ filter: /\.ya?ml$/ }, args => {
    let YAML = require('js-yaml')
    let source = fs.readFileSync(args.path, 'utf8')
    try {
      let contents = JSON.stringify(YAML.safeLoad(source), null, 2)
      return { contents: `module.exports = ${contents}` }
    } catch (e) {
      return {
        errors: [{
          text: (e && e.reason) || (e && e.message) || e,
          location: e.mark && {
            line: e.mark.line,
            column: e.mark.column,
            lineText: source.split(/\r\n|\r|\n/g)[e.mark.line],
          },
        }],
      }
    }
  })
}
```

### CoffeeScript loader

```js
let coffeeLoader = plugin => {
  plugin.setName('coffee-loader');
  plugin.addLoader({ filter: /\.coffee$/ }, args => {
    let CoffeeScript = require('coffeescript')
    let source = fs.readFileSync(args.path, 'utf8')
    try {
      return { contents: CoffeeScript.compile(source) }
    } catch (e) {
      return {
        errors: [{
          text: (e && e.message) || e,
          location: e.location && {
            line: e.location.first_line,
            column: e.location.first_column,
            length: e.location.last_column - e.location.first_column + 1,
            lineText: source.split(/\r\n|\r|\n/g)[e.location.first_line],
          },
        }],
      };
    }
  })
}
```
