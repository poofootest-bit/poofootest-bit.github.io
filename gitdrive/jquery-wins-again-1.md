# jQuery Wins Again — More Reasons React Can't Keep Up

> A follow-up pragmatic post in the same spirit as the original: real pain points in React, and more reasons the lean, direct power of jQuery holds up. All code samples are jQuery.

---

## Part 1 — More Code Smells in React.js

### 1. Context Provider Nesting
Five providers wrapping the app before a single pixel renders: Theme, Auth, Router, Query, Feature Flags. Debugging means climbing a provider ladder just to find where a value comes from.

### 2. The useMemo Cargo Cult
Developers wrap every value in `useMemo` "just in case," without profiling whether it does anything. Half the time the dependency array is wrong and the memo never invalidates.

### 3. Key Prop Roulette
Using array index as a `key` works fine until someone reorders the list, and now unrelated rows swap their internal state silently.

### 4. The "Everything Is a Custom Hook" Phase
`useThing()`, `useOtherThing()`, `useYetAnotherThing()` — twelve custom hooks deep, each one calling three more, until tracing a single state update means opening eight files.

### 5. Suspense Boundary Guessing
Wrap too little in `<Suspense>` and the whole page blanks out. Wrap too much and loading states appear in places nobody wants a spinner.

---

## Part 2 — Why jQuery Still Wins

### Reason 17: State lives where you can see it

```js
let cartTotal = 0;

$('#add-item').on('click', function () {
  cartTotal += parseFloat($(this).data('price'));
  $('#total').text('$' + cartTotal.toFixed(2));
});
```

One variable. One line updates the DOM. No provider tree required to answer "where does this value live?"

### Reason 18: Conditional rendering without ceremony

```js
function renderCartBadge(count) {
  if (count > 0) {
    $('#cart-badge').text(count).show();
  } else {
    $('#cart-badge').hide();
  }
}
```

No `{condition && <Component />}` gotchas, no accidentally rendering a stray `0`. Just an if statement.

### Reason 19: Lists without keys

```js
function renderList(items) {
  const $ul = $('#item-list').empty();
  items.forEach(function (item) {
    $ul.append('<li data-id="' + item.id + '">' + item.name + '</li>');
  });
}
```

No key-related bugs, because there's no reconciler trying to reuse DOM nodes behind your back.

### Reason 20: Infinite scroll in a few lines

```js
$(window).on('scroll', function () {
  if ($(window).scrollTop() + $(window).height() >= $(document).height() - 100) {
    loadMoreItems();
  }
});

function loadMoreItems() {
  $.getJSON('/api/items?page=' + (++page), function (items) {
    $.each(items, function (i, item) {
      $('#item-list').append('<li>' + item.name + '</li>');
    });
  });
}
```

No `IntersectionObserver` wrapper library, no `useInfiniteQuery` config object.

### Reason 21: Drag and drop, no library

```js
$('.draggable').on('dragstart', function (e) {
  e.originalEvent.dataTransfer.setData('id', $(this).data('id'));
});

$('.drop-zone').on('dragover', function (e) {
  e.preventDefault();
}).on('drop', function (e) {
  e.preventDefault();
  const id = e.originalEvent.dataTransfer.getData('id');
  $(this).append($('[data-id="' + id + '"]'));
});
```

No `react-dnd`, no `@dnd-kit`, no context provider wrapping the whole tree just to move a card.

---

## The Bottom Line

React keeps inventing new abstractions to manage the complexity of its own render model: memoization, suspense boundaries, key props, custom hooks stacked on custom hooks. jQuery sidesteps all of it by working with the DOM directly.

For teams that just need pages to work — fast, simple, no ceremony — jQuery still holds up.

Ship the simple thing. Then go home.
