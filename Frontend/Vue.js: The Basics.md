## Overview
Vue.js is a JavaScript UI framework. Unlike other monolithic frameworks, Vue is designed from the ground up to be incrementally adoptable. The core library is focused on the view layer only, and is easy to pick up and integrate with other libraries or existing projects. Although not as popular as its main competitors - Angular and React - the framework has been gaining popularity due to its simplicity and adoptability.
Vue is a **progressive framework**. In short, that means Vue has:
* Lean core that works well alone
* Official support libraries that solve common needs
* Opinionated but optional build toolchain

Why Vue?
* Approachability - the basic HTML+CSS+JS set is all you need to get started
* Versatility - since Vue provides only the tools to build the view layer, you aren't locked into using a certain architecture or a certain library
* Performance - due to being well-optimized, Vue renders faster than most of its contestants; it's way of re-rendering the view also means you need to spend less time optimizing your app 

## Concepts
Although not strictly associated with the [MVVM](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93viewmodel) pattern, Vue’s design was partly inspired by it. As a convention, the `vm` variable is often used to refer to the _Vue instance_: 
```javascript
var data = { name: 'Nick' }
var vm = new Vue({
  el: '#example',
  data: data
})
```
Now you just need an _element_ to bind that instance to:
```html
<div id="example">
  Hello, {{ name }}
</div>
```
and voilà! You've got yourself a Vue app! But wait, there's more to it than that.
By using the `$` prefix you can address the instance's fields: 
```javascript
vm.$data === data // true
vm.$el === document.getElementById('example')  // true
``` 
as well as use the instance methods, such as `$watch()`:
```javascript
vm.$watch('name', function (newVal, oldVal) {
  // this callback will be called when `vm.name` changes
})
```
Each Vue instance also goes through a series of steps during it's initialization, each of these steps has a _lifecycle hook_ associated with it, such as `created`, `mounted`, `updated` and `destroyed`:
```javascript
var vm = new Vue({
  data: {
    name: 'Nick'
  },
  created: function () {
    // `this` points to the vm instance
    console.log('name is: ' + this.name)
  }
})
// -> "name is: Nick"
```
Vue templates use the standard "Mustache" syntax (same as Angular). Vue has a number of convenient directives (also same as Angular), which use the `v-` prefix :
```html
<span v-once>This will never change: {{ msg }}</span>

<ul id="example">
  <li v-for="item in items">  <!-- v-for directive renders the element once for each item in the items array -->
    {{ item.message }}
  </li>
</ul>
```
This one, for example, will not allow the view inside this element to update, even once the `msg` changes.
"Mustaches" cannot be used inside HTML attributes, so the `v-bind` directive should be used:
```html
<div v-bind:id="dynamicId"></div>
```
Some directives, such as `v-bind` or `v-on` accept an _argument_, which comes after a colon right after the directive's name. Some directives can have _modifiers_ that allow you to do some nifty things, such as `.prevent`:
```html
<form v-on:submit.prevent="onSubmit"></form>
```
that, aside from the action assigned to `v-on:submit`, also calls the `event.preventDefault()`. 
Vue also allows you to define _filters_, that can be used for text formatting: 
```javascript
new Vue({
  // ...
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
})
```
Filters are appended to the end of the JavaScript expression, denoted by the “pipe” symbol.
Several most used directives - `v-bind` and `v-on` - have _shorthands_, which are entirely optional, but can save you a bit of time:
```html
<!-- full syntax -->
<a v-bind:href="url"></a>
<!-- shorthand -->
<a :href="url"></a>

<!-- full syntax -->
<a v-on:click="doSomething"></a>
<!-- shorthand -->
<a @click="doSomething"></a>
``` 
In-template expressions are very convenient, but they cater for non-complicated operations exsclusively. Putting too much logic into your templates can make them bloated and hard to maintain. That's where _computed expressions_ come into play: 
```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```
```javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
})
```
"What's the difference between computed expressions and _methods_?" you might ask. 
```javascript
// in component
methods: {
  reverseMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```
The thing is, computed properties are _cached based on their dependencies_. A computed property re-evaluates only in case when some of its dependencies are changed, opposed to methods, which always run the function whenever the re-render occurs. 
_Components_ allow you to extend basic HTML elements to encapsulate reusable code.At a high level, components are custom elements that Vue’s compiler attaches behavior to. Use `Vue.component()` to register a global component:
```html
<div id="example">
  <my-component></my-component>
</div>
```
```javascript
// register
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})
// create a root instance
new Vue({
  el: '#example'
})
```
Which will render
```html
<div id="example">
  <div>A custom component!</div>
</div>
```
You can also register a component locally:
```javascript
new Vue({
  // ...
  components: {
    // <my-component> will only be available in parent's template
    'my-component': {
      template: '<div>A custom component!</div>'
    }
  }
})
```

## Requirements
In order to start working with Vue all you need is to install Vue. You can either use the [CDN distributive](https://unpkg.com/vue) (Vue is a very lightweight library so it's absolutely fine), or install it via `npm install vue` or `bower install vue`. Vue provides an [official CLI](https://github.com/vuejs/vue-cli) that allows you to quickly set up and easily manage your future project.
