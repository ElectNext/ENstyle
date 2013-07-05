# CSS Styleguide

This is the ElectNext CSS styleguide. It's based on the awesome 
[GitHub CSS Styleguide](https://github.com/styleguide/css).

Before reading this, you should have a general understanding for *specificity* and the
[SCSS](http://sass-lang.org) syntax.

## Coding Style
  * Use soft-tabs with a **2** space indent
  * Put spaces after `:` in property declarations.
  * Put spaces before `{` in rule declarations.
  * Use hex color codes `#000` unless using rgba.
  
  Here is good example syntax:
  `
  .styleguide-format {
    border: 1px solid #0f0;
    color: #000;
    background: rgba(0,0,0,0.5);
  }
  `
  
## Pixels vs. Ems
We use `em` for `font-size`. The `<body>` should have a `font-size: 16px;`. All other font-sizes should inherit from that.

## Class naming conventions
Never reference js- prefixed class names from CSS files. js- are used exclusively from JS files.

Use the is- prefix for state rules that are shared between CSS and JS.

**We haven't been doing this. Let's start now.**

## Specificity (classes vs. ids)
Elements that occur **exactly once** inside a page should use IDs, otherwise, use classes. When in doubt, use a class name.

* **Good** candidates for ids: header, footer, modal popups.
* **Bad** candidates for ids: navigation, item listings, pretty much everything else.

When styling a component, start with an element + class namespace (prefer class names over ids), prefer direct descendant selectors by default, and use as little specificity as possible. Here is a good example:

```
  <ul class="category-list">
  	<li class="item">Category 1</li>
    <li class="item">Category 2</li>
    <li class="item">Category 3</li>
  </ul>
```

```
  ul.category-list { // element + class namespace

    &>li { // direct descendant selector > for list items
      list-style-type: disc;
    }

    a { // minimal specificity for all links
      color: #ff0000;
    }
  }
```

### CSS Specificity Guidelines

* If you must use an id selector (`#selector`) make sure that you have no more than _one_ in your rule declaration. A rule like `#header .search #quicksearch { ... }` is considered harmful.
* When modifying an existing element for a specific use, try to use specific class names. Instead of `.listings-layout.bigger` use rules like `.listings-layout.listings-bigger`. Think about greping your code in the future.


## CSS for 3rd party JS widgets
When styling a javascript widget that lives on someone else's site, defensive CSS rules apply. Also note that we are using pure CSS for our widgets (not SASS).

* Prefix **everything** with `enxt-`. We are in a stranger's DOM, assume they are out to harm us.
* Be very specific. For example:

```
  #enxt-widgets ul.enxt-tab-list li.enxt-tab-list-item a.enxt-tab-link:hover,
  #enxt-widgets ul.enxt-tab-list li.enxt-tab-list-item a.enxt-tab-link.enxt-tab-list-active {
    text-decoration: none;
    color: #fff !important;
    background-color: #4d4d4d;
    opacity: 1;
  }
``` 
(Note that when you are very specific, `!important`s are ok.)

