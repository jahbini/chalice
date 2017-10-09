# Halvalla

## The land where HTML deamons are conjured, instantiated and summoned into Three Domains

Valhalla began as an attempt to use Teact to create a react based application without using pointy brackets or back-ticks.  Rude things that bite the syntax and uglify the landscape.

Halvalla is intended to allow a teacup syntax html scripting notation to run unchanged in any of three back-end rendering engines:  React and Mithril virtual DOMs and HTML text. 
i
It started out as teact, but halvalla put back in the missing sweetness of the teacup rendering engine.

The following syntax is supported.  Your mileage may vary.

# It used to be Teact

It's [better than cjsx](#how-is-this-better-than-cjsx).

Build React or Mithril element trees by composing functions.  
You get full javascript control flow, and minimal boilerplate.
It's also quite simple (not really), just a thin (as possible) wrapper around [React.createElement](https://facebook.github.io/react/docs/top-level-api.html#react.createelement) or Mithril

## Usage
```coffee
{tag} = require 'halvalla-react'

tag 'div', '#root.container', ->
  unless props.signedIn
    tag 'button', onClick: handleOnClick, 'Sign In'
  tag.text 'Welcome!'
```

Transforms into:

```coffee
React.createElement('div',
  {id: root, className: 'container'}, [
    (props.signedIn ? React.createElement('button',
      {onClick: handleOnClick}, 'Sign In'
    ) : null)
    'Welcome!'
  ]
)
```

Use it from your component's render method:
```coffee
{div,Component} = require 'halvalla-mithril'

class Widget extends Component
  render: ->
    div className: 'foo', =>
      div, 'bar'
```

Or in a [stateless component](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions):

```coffee
module.exports = (props) ->
  tag 'div', className: 'foo', ->
    tag 'div', props.bar
```

### Nesting Components

`crel` is just a thin wrapper around [React.createElement](https://facebook.github.io/react/docs/top-level-api.html#react.createelement),
so you can pass it components instead of crel names:

```coffee
class DooDad extends Component
  render: ->
    crel 'div', className: 'doodad', =>
      crel 'span', @props.children

class Widget extends Component
  handleFiddle: =>
    # ...

  render: ->
    crel 'div', className: 'foo', =>
      crel DooDad, onFiddled: @handleFiddle, =>
        crel 'div', "I'm passed to DooDad.props.children"

```

If you need to build up a tree of elements inside a component's render method, you can
escape the element stack via the `pureComponent` helper:

```coffee
{crel, pureComponent} = require 'teact'

Teas = pureComponent (teas) ->
  teas.map (tea) ->
    # Without pureComponent, this would add teas to the element tree
    # in iteration order.  With pureComponent, we just return the reversed list
    # of divs without adding the element tree.  The caller may choose to add
    # the returned list.
    crel 'div', tea
  .reverse()

class Widget extends Component
  render: ->
    crel 'div', Teas(@props.teas)
```

### Sugar Syntax
Teact exports bound functions for elements, giving you options for
terser syntax if you're into that:

```coffee
T = require 'teact'

T.div className: 'foo', ->
  T.text 'Blah!'
```

or the Teacup / CoffeeCup signatures:

```coffee
{div, text} = require 'teact'

div '.foo', ->
  text 'Blah!'
```

## Performance

A [super-basic performance test](test/benchmarks/index.coffee) suggests that teact has no discernible impact on React rendering performance:

```sh
$ npm run benchmark

> native x 5,197 ops/sec ±3.30% (76 runs sampled)
> teact x 5,339 ops/sec ±2.23% (82 runs sampled)
> Fastest is teact,native
```

It's also lightweight, at 5KB minified, 2KB gzipped.  

## How is this better than CJSX?

- Familiar control flow with branching and loops.  See examples above.
- No transpiler to [maintain](https://github.com/jsdf/coffee-react/issues/28).
- No [extraneous enclosing tags](https://babeljs.io/repl/#?experimental=false&evaluate=true&loose=false&spec=false&code=%3Cdiv%3E%3C%2Fdiv%3E%0A%3Cdiv%3E%3C%2Fdiv%3E) required:

  ```coffee
  renderHeader: ->
    unless @props.signedIn
      crel 'a', href: '...', 'Sign in'
    crel 'h1', 'Tea Shop'
  ```

  Just works.
- [Nice syntax errors](https://github.com/jsdf/coffee-react/issues/32).
- Half the lines of code. Those closing tags really add up.

Other folks have [reached similar conclusions](https://slack-files.com/T024L9M0Y-F02HP4JM3-80d714).  They were later [bit by using the React API directly](https://github.com/planningcenter/react-patterns#jsx) when the implementation changed.  A thin wrapper like Teact should minimize this risk.

## Legacy

[Markaby](http://github.com/markaby/markaby) begat [CoffeeKup](http://github.com/mauricemach/coffeekup) begat
[CoffeeCup](http://github.com/gradus/coffeecup) and [DryKup](http://github.com/mark-hahn/drykup) which begat
[Teacup](http://github.com/goodeggs/teacup) which begat **Teact**.

## Contributing

```sh
$ git clone https://github.com/hurrymaplad/teact && cd teact
$ npm install
$ npm test
```
