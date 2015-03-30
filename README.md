# jquery.waitforChild

Operate on matching children of a given element. If no matching children are found, it will set an observer to run the desired operation once (if) the element gets inserted afterwards.

## How to use

Let's say you have a container and this container has children

```html
<ul id="mylist">
    <li class="first">First item</li>
    <li class="second">Second item</li>
    <li class="third">Third item</li>
</ul>
```

It's trivial to traverse the children to add an extra class, let's say "pink". 

```js
$('#mylist').find('li').addClass('pink');
```

And if you wanted to operate only on the element that has the className "second" this is trivial too

```js
$('#mylist').find('li.second').addClass('pink');
```

But what would happen if you wanted to set handlers, or perform DOM manipulation on elements that doesn't yet exist? Let's say you have the empty container:

```html
<ul id="mylist">
</ul>
```

And you know that, eventually, another function, handler or event will append the list elements

```js
$('#mylist').append('<li class="first">First item</li>');
$('#mylist').append('<li class="second">Second item</li>');
$('#mylist').append('<li class="third">Third item</li>');
```

But you don't know when or from where. With **jquery.waitforChild** you just can set a listener on the container:

```js
$('#mylist').waitforChild(function(child) {
    child.addClass('pink');
},'li.second');
```

Now, whenever a matching childNode is inserted on *#mylist*, the function will be invoked on it. 

### Apply the onFound function only once

There are some cases in which you will want to act on the first element to match the selection. If that's the case, you can pass a third parameter:

```js
$('#mylist').waitforChild(function(child) {
    child.addClass('pink');
},'li.second',true);
```

Which will operate only on the first matching element ever appended to the container.

### Passing the parameters as an object

The plugin also accepts an object as the only argument, in the form:

```js
$('#mylist').waitforChild({
    onFound: function(child) {
        child.addClass('pink');
    },
    querySelector: 'li.second',
    once: true
});
```

### Chaining methods

The plugin returns the original element, so you are free to chain methods as with other jQuery functions

```js
$('#mylist').waitforChild({
    onFound: function(child) {
        child.addClass('pink');
    },
    querySelector: 'li.second',
    once: true
}).addClass('niceclass');
```


### Why can't I just delegate on the parent object?

Delegation is useful if you want to take advantage of natural event bubbling, and if you're already using jQuery you'll be able to use it even in older browsers. However, there are cases in which delegation won't solve your problem. For example, when you want to [apply KnockOut's declarative bindings](http://knockoutjs.com/documentation/observables.html), or render a [Backbone View](http://backbonejs.org/#View).

Moreover, abusing event delegation makes it harder to keep track of where is a given behavior triggered, and you risk assigning it multiple times if you declare the delegated event in the wrong place. 


### Caveats

The [MutationObserver](https://developer.mozilla.org/en-US/docs/Web/API/MutationObserver) object needs an existing Node to operate on. Therefore, you can't invoke *jquery.waitforChild* on an element which isn't in the DOM.

Also, older browsers don't support this feature. Pay a visit to [Can I Use](http://caniuse.com/#feat=mutationobserver) to check if your browser is supported.
