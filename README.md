# MPG CSS

This is a methodology for organizing css that has worked quite well over a large variety of projects. The name stands for **M**odule **P**age **G**lobal, which is how chunks of css are organized according to this approach.

But why a different CSS organization system when there are so many already out there? Let's get into it.

### Scoping

One of the biggest potential issues with CSS is what I like to call _scope leaks_. These happen when you write styles for one specific section of a page, but because of the way you made the selection, the styles also affect elements on other random parts of the page. This is a really bad problem, as most of the time you don't recognize it until someone else does. A primary goal of MPG is to scope styles tightly and prevent scope leaks.

### DRY Code

In programming, ["Don't Repeat Yourself" (DRY)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) is a manifesto that hardly any programmers disagree with. The reasoning behind this is very sound: repeating yourself is a waste of time, and allows more opportunity for error when you change once repeated instance but not the other. In css, this is also very important, and has become much easier to do with the advent of great css preprocessors which allow variables, nesting, mixins, and more.

There are many CSS systems out there at the moment that involve a large amount of repetition. MPG very specifically tries to avoid any type of repetition.

### Clean Markup

It is nearly universally agreed among programmers that clean code is much better than messy code. This should be the same for html markup. Adding bunches of classes and ids with extra symbols in them to markup makes the markup less clean and _more difficult to read and deal with_. HTML5 was made to include enough vocabulary to be able to define most parts of a website with it's semantics. This should be taken advantage of.

MPG strongly adheres to the philosophy that clean, readable, and semantic markup is of paramount importance to any website, and that the minimal amount of extra words and classes should be added to html in order to get the website styled correctly.

# The Method

Ok, enough preaching, let's get into the real stuff. MPG divides your css into three levels: global, page-specific, and module-specific. Let's break each of these down individually.

### Global

Some styles are used in multiple places across a website. For example, your `h1` element might consistently look the same (and it should!), or you might have a `.box` class that wraps a piece of content in an elegant box. These are good candidates for global styles. Global styles are great, they give a sense of unity to your site, and should be used frequently. If you've seen something like a branding manual or style guide, these represent global styles.

Since they are intended to be repeated, _global styles should always be classes, never ids_. In the same vein, styles should only be defined as global if and only if they appear on multiple different pages. Finally, to make it clear that a global style is being used, all global classnames should be prefixed with `g-`, as in `.g-box`. If you are styling a raw element like `ul` or `h1` with a global base style, however, it's not necessary to add an extra class.

### Page-Specific

A "page" can be defined in one of two ways. If you are working with a traditional website that loads new pages on link clicks, it's as easy as that. If you have a single page app that you are using some sort of javascript MV* for, you can define a "page" as a major view that you would never see more than one of on a page at the same time. For example, an item view that's part of a collection would not qualify, as you can and probably will see more than one of them on a page. However, it is possible that the collection's view could be defined as a page, as long as you don't have a collection of collections. You get the idea.

Page-specific styles are styles that _do not apply globally_, but do apply locally to all elements within a specific page. For example, you might have one page where there is a unique color to your header elements across the page.

All pages should have an id with the page's name on the `body` element, to allow for simple scoping across the page. If you are making a single-page app with javascript, the id can apply to the view's wrapper element instead. _All page-specific styles should be scoped under this id_. If this cannot work, they are global styles. **Any CSS that you write that is not global should be scoped under a page's id**.

### Module-Specific

This is the most specific level of selection. A module is defined as a section within a page. For example, on your about page, you might have a portion that contains an introductory paragraph, then a section that has some of your company's staff, and a section that has some of the clients you've worked with. Each of these sections would be defined as it's own module, and _since there can only be one of them per page, these are also marked with ids_.

If you are writing styling that applies only to that specific module, it should be nested under at least 2 levels of ids -- the page id and the module id. This is the default place you should put code if you aren't sure, as _there is no chance of a scope leak for code here_. Even if you have a module that is named the same thing coincidentally on another page, it will still not leak because it's scoped under the page id as well. If later on you notice that the same style is actually used elsewhere on the same page, you can pull it up to a page-specific style, and if you notice it being used on other pages, you can pull it up to global. This is the safest route to take, that prevents the most css errors. As soon as you find the need to use a block of css outside of a module, pull it up to a page-level style. And if you need to again outside the page, pull it up to global. If you are copy-pasting blocks of css in order to get around this problem _you are doing it wrong_.

# An Example

With the theory out of the way, let's look at an example site. We'll make this site with 2 pages, a home page and an about page. Here's the markup:

### Index Page

```html
<body id='index'>

  <div id='banner'>
    <h1>Welcome to the Example Page!</h1>
    <p>Aenean lacinia bibendum nulla sed consectetur. Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.</p>
  </div>

  <div id='photos'>
    <img class='g-full-width' src='example.jpg' />
    <img class='g-half-width left' src=example2.jpg />
    <img class='g-half-width right' src=example3.jpg />
  </div>

</body>
```

### About Page

```html
<body id='about'>

  <h1>About Us</h1>

  <div id='intro'>
    <img class='g-full-width' src="us.jpg" />
    <p>Vivamus sagittis lacus vel augue laoreet rutrum faucibus dolor auctor. Etiam porta sem malesuada magna mollis euismod.</p>
  </div>

  <div id='staff'>
    <img src="staff/john.jpg" />
    <img src="staff/kate.jpg" />
    <img src="staff/adam.jpg" />
  </div>
</body>
```

Ok, so this is some fairly straightforward stuff here, nothing crazy. Note how each page has a body id, and each section on the page has a div wrapping it with an id that describes what it contains. Also notice how this is very easy to read. Without even seeing any part of the styling, just by reading the html, you can imagine how this page might look. Now let's look at how the css structure might look. I won't be including any actual styles here since this is arbitrary, just the selectors, so this will be sort of a pseudo-css example. The selectors will also be nested as stylus or sass would do. I prefer splitting files into their page levels as well and concatenating them on build, so I'll split them like this, although if you want you could write everything in a single file.

_global.css_

```
h1
p
.g-full-width
.g-half-width
```

Notice how there's no nesting here. The only time you should see nesting in the global css file is if you are handling some sort of hover or active properties, or if you have a global style the comprises multiple elements (which, to be fair, does definitely happen, like the media class). These are all resuseable styles across the entire site.

_index.css_

```
#index
  .left
  .right
  #banner
    h1
  #photos
    img
```

So here we see a nice clean structure. You'll notice that perhaps the h1 in the banner section is a little different from the global style, so we have an override for this in the banner section. If it was the exact same, we wouldn't even need to select anything in the banner section.

_about.css_

```
#about
  #intro
    p
  #staff
    img
```

Again, quite simple here. We have an override for the intro paragraph (perhaps a little larger text), and styles for the staff images that only apply to images there (perhaps they would have a border-radius and be floated so they align horizontally). The rest is covered by the global styles. Obviously this is a very simple example, but should be enough to get the idea across!

Another nice benefit you might have noticed while reading is the use of ids. A lot of the time, people will tell you not to use ids, just because it's possible to use them wrong where it's not possible with classes. But ids serve a few needs here. First, they force you to think about re-use, which is hugely important. If you find yourself putting more than one id on a page (which is invalid html), you know you might have categorized incorrectly and should re-organize. The ids also act almost as headlines within your stylesheets. They sit at the base or second level consistently, and are used as categories. It's much quicker to jump to the section you need to within a larger stylesheet by scanning the ids you know will be there than looking for arbitrary selectors. I think of the ids in my css as I think of headlines in my writing, and conveniently, they even visually differentiate themselves as such.

### Comparison to Other Systems

There are a few other popular methodologies for writing css out there, but they all suffer from similar issues. We discussed at the beginning where the strengths of MPG are, and these are almost all where the weaknesses of other systems are. Most systems (smacss, bem, oocss) do not use nesting at all. Instead of nesting, these systems propose that you name your selectors in a way that implies nesting, but then ignore the natural cascading of css entirely.For example, you might write normal css or MPG like this:

```
#menu
  li
```

Whereas with BEM for example, it's all classes, and nesting means repeating the word in the class, like this:

```
.menu
.menu__item
```

Not only is this gratuitous repetition, it's ignoring the natural power of css and the natural semantics of html, and is much more work to write and harder to read. And it gets worse and worse the more levels of nesting you have. Remember, CSS stands for Cascading style sheets -- why ignore the main strength of css, then replicate it yourself in a less efficient manner?

### Try It!

MPG helps to create clear, readable, and modular html and css. Many other systems ignore the cascade and nesting and for that reason you have to do more work and repeat yourself. This is not the case with MPG, which takes advantage of the natural semantics of html and cascading properties of css to allow clean and quick styling. But the light structure it imposes also creates clear reuseable modules and safety from scope leaks. Give it a shot for your next project and see what you think!
