# GPS CSS Methodology

[![Gitter Chat](https://badges.gitter.im/jescalan/gps.svg)](https://gitter.im/jescalan/gps)

This is a methodology for organizing CSS that has worked quite well over a large variety of projects. The name stands for **G**lobal **P**age **S**ection, which is how chunks of CSS are organized according to this approach.

But why a different CSS organization system when there are so many already out there? Let's get into it.

### Scoping

One of the biggest potential issues with CSS is what I like to call _scope leaks_. These happen when you write styles for one specific section of a website, but because of the way you made the selection, the styles also affect elements on other random parts of the site. This is a side effect of CSS being written in the global scope by default. This is a really bad problem, as most of the time you don't recognize it until someone else does. A primary goal of GPS is to scope styles tightly and prevent scope leaks.

### DRY Code

In programming, ["Don't Repeat Yourself" (DRY)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) is a manifesto that hardly any programmers disagree with. The reasoning behind this is very sound: repeating yourself is a waste of time, and allows more opportunity for error when you change one repeated instance but not the other. In CSS, this is also very important, and has become much easier to do with the advent of great CSS preprocessors which allow variables, nesting, mixins, and more.

There are many CSS systems out there at the moment that involve a large amount of repetition. GPS very specifically tries to avoid any type of repetition.

### Clean Markup

It is nearly universally agreed among developers that clean and readable code is much better than messy code. This applies just as much to HTML and CSS. Adding bunches of classes and ids with extra symbols in them to markup makes it _more difficult to read and deal with_. HTML5 was made to include enough vocabulary to be able to define most parts of a website with it's semantics. This should be taken advantage of.

GPS strongly adheres to the philosophy that clean, readable, and semantic markup is of paramount importance to any website, and that a minimal amount of extra words and classes should be added to HTML in order to get the website styled correctly.

# The Method

Ok, enough preaching, let's get into the real stuff. GPS divides your CSS into three levels:

- global
- page-specific
- section-specific

Let's break each of these down individually.

### Global

Some styles are used in multiple places across a website. For example, your `h1` element might consistently look the same (and it should!), or you might have a `.box` class that wraps a piece of content in an elegant box. These are good candidates for global styles. Global styles are great, they give a sense of unity to your site, and should be used frequently. If you've seen something like a brand manual or style guide, these represent global styles.

Since they are intended to be repeated, _global styles should always be classes, never ids_. In the same vein, styles should only be defined as global if and only if they appear on multiple different pages. Finally, to make it clear that a global style is being used, all global class names should be prefixed with `g-`, as in `.g-box`. If you are styling a raw element like `ul` or `h1` with a global base style, however, it's not necessary to add an extra class.

> If you are working on a large website or app, it is extremely helpful to create a reference page for all global styles. This makes it easier for developers to start working on the project with a good understanding of what these styles are and how they are used.

### Page/View Specific

A "page" (or "view") can be defined in one of two ways. If you are working with a traditional website that loads new pages on link clicks, it's as easy as that. If you have a single-page app that you are using some sort of JavaScript MV* for, you can define a "page" as a major view that you would never see more than once on a page at the same time. For example, an item view that's part of a collection would not qualify, as you can and probably will see more than one of them on a page. However, it is possible that the collection's view could be defined as a page, as long as you don't have a collection of collections. You get the idea.

The basic criteria that define a page is as such:

1. It is a major view, it would have it's own page in a design mock
2. It's not even possible that you would ever find more than one in the window at the same time
3. It contains one or more "sections" (discussed below)

Page-specific styles are styles that _do not apply globally_, but do apply locally to all elements within a specific page. For example, you might have one page where there is a unique color to your header elements across the page.

All pages should have an id with the page's name on the `body` element, to allow for simple scoping across the page. If you are making a single-page app with JavaScript, the id can apply to the view's wrapper element instead. _All page-specific styles should be scoped under this id_. If this scope doesn't work for a style you are considering, it is probably a global style. **Any CSS that you write that is not global should always be scoped under a page or view's id**.

Page ids are also typically prefixed with `p-` to ensure that there are no conflicts with anchor links or internal sections on other pages. For example, if you did not use the `p-` prefix, you could have a page called `company`, which would be defined globally as a page level scope, and a section on your about page called `company` that is an anchor link. In this case you would have a scope leak. Prefixing page-level styles would ensure that this doesn't happen. Generally, any time you are writing a style that is globally scoped it should be prefixed to avoid accidental conflicts. This is why global styles start with `g-` and page styles start with `p-`.

### Section-Specific

This is the most specific level of selection. A section is defined as a section within a page. For example, on your about page, you might have a portion that contains an introductory paragraph, then a section that has some of your company's staff, and a section that has some of the clients you've worked with. Each of these sections would be defined as it's own section, and _since there can only be one of them per page, these are also marked with ids_.

If you are writing styling that applies only to that specific section, it should be nested under at least 2 levels of ids – the page id and the section id. This is the default place you should put code if you aren't sure, as _there is no chance of a scope leak for code here_. Even if you have a section that is named the same thing coincidentally on another page, it will still not leak because it's scoped under the page id as well. If later on you notice that the same style is actually used elsewhere on the same page, you can pull it up to a page-specific style. And if you notice it being used on other pages, you can pull it up to global. This is the safest route to take and prevents the most CSS errors. As soon as you find the need to use a block of CSS outside of a section, pull it up to a page-level style. And if you need it again outside the page, pull it up to global. If you are copy-pasting blocks of CSS in order to get around this problem _you are doing it wrong_.

# An Example

With the theory out of the way, let's look at an example site. This site consists of 2 pages, a Home page and an About page. Here's the markup:

### Home Page

```html
<body id='p-index'>

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
<body id='p-about'>

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

Ok, so this is some fairly straightforward stuff here, nothing crazy. Note how each page has an id on `body`, and each section on the page has a `div`wrapping it with an id that describes what it contains. Also notice how this is very easy to read. Without even seeing any part of the styling, just by reading the HTML, you can imagine how this page might look. Now let's look at how the CSS structure might look. I won't be including any actual styles here since this is arbitrary, just the selectors, so this is sort of a pseudo-css example. The selectors will also be nested as Stylus or SASS would do. I prefer splitting files into their page levels as well and concatenating them on build, so I'll split them like this, although if you want you could write everything in a single file.

_global.css_

```css
h1
p
.g-full-width
.g-half-width
```

Notice how there's no nesting here. The only time you should see nesting in the global CSS file is if you are handling some sort of hover or active properties, or if you have a global style that comprises multiple elements (which, to be fair, does definitely happen, like the media class). These are all reuseable styles across the entire site.

_index.css_

```css
#p-index
  .left
  .right
  #banner
    h1
  #photos
    img
```

So here we see a nice clean structure. You'll notice that perhaps the `h1`in the banner section is a little different from the global style, so we have an override for this in the banner section. If it was the exact same, we wouldn't even need to select anything in the banner section.

_about.css_

```css
#p-about
  #intro
    p
  #staff
    img
```

Again, quite simple here. We have an override for the intro paragraph (perhaps a little larger text), and styles for the staff images that only apply to images there (perhaps they would have a border-radius and be floated so they align horizontally). The rest is covered by the global styles. Obviously this is a very simple example, but should be enough to get the idea across!

Another nice benefit you might have noticed is the use of ids. A lot of the time, people will tell you not to use ids, just because it's possible to use them wrong where it's not possible with classes. But ids serve a few needs here. First, they force you to think about re-use, which is hugely important. If you find yourself putting more than one id on a page (which is invalid HTML), you know you might have categorized incorrectly and should re-organize. The ids also act almost as headlines within your stylesheets. They sit at the base or second level consistently, and are used as categories. It's much quicker to jump to the section you need to within a larger stylesheet by scanning the ids you know will be there than looking for arbitrary selectors. I think of the ids in my CSS as I think of headlines in my writing, and conveniently, they even visually differentiate themselves as such.

# Media Queries

Writing responsive CSS is no longer optional, or an enhancement to the page, it is now an essential for all websites. So it's only fair that we discuss it here as well, since media queries also must fit well into your CSS' structure in order for you to maintain sanity on large builds.

### Rule Placement

The general GPS rule for media queries is that **you should almost never have a selector nested inside a media query**. That means that all your media queries should be placed inline, nested along with all the other CSS. Of course, you need to be using a preprocessor that supports nested rules and nested media queries in order to make this happen, but those are [not too tough](http://cssnext.io/features/#nesting) to come across these days.

Why? Because responsive styles are no different than normal styles. They are styles that are applied to the element in question, and therefore should be grouped together with that element. So for example, this is the right approach:

```css
.foo {
  color: red;

  @media (min-width: 30em) {
    color: blue;
  }
}
```

This, on the other hand, is the wrong approach:

```css
/* THIS IS WRONG. DO NOT DO THIS. */
.foo {
  color: red;
}

@media (min-width: 30em) {
  .foo {
    color: blue;
  }
}
```

This approach provides the obvious advantage of grouping your styles together in the same place so that any developer knows where to look. It also virtually eliminates the chances of an issue with specificity. For example, in the above example if the media block was places above the `.foo` block, it would not work. Once you get to more complicated nested selectors inside media queries, it gets even more difficult to avoid issues with specificity.

It makes it a little easier to make the mental shift to this way of using media queries if you think of them exactly in the same way that you think of normal styles, because in reality, that's what they are. You would never write a selector block, then another one further down to apply more styles, you would group them together. And media queries are just the same.

### Breakpoints

In the ancient past days when smartphones and tablets were newfangled gadgets, it was permissible and in fact frequently advised that you create and utilize "breakpoints" for your media queries. In these days, it was common to have specific breakpoints that are used across the site, "desktop", "tablet", and "mobile", for example. And honestly, at the time it worked fine because those were the only devices that existed. But now that we have devices with screens of all sizes, the need for any type of device-specific breakpoints is gone.

GPS advises that you create media queries **around the content rather than the device / screen size**. So generally you will size the browser window down, and just before something starts to look awkward, add a media query at that point to fix it. It doesn't matter if the number is clean and even, or how many other elements need to have a media query at that same number, just run it. Then continue doing so for all other elements on the page.

Often times this approach will bother people because there's something mentally nice about having just a few consistent and cleanly named breakpoints. Us humans have a way of enjoying order and cleanliness. But in this case creating a breakpoint whenever one is needed is actually the superior approach, as logically there is no benefit to limiting your number of breakpoints. In fact there is a disadvantage, which is that you need to either overcompensate at each breakpoint to ensure that the content is ok until the next one, or have areas in between breakpoints in which the content looks bad.

And for those concerned about CSS size, [gzip eliminates this issue](https://github.com/MoOx/postcss-cssnext/issues/27#issuecomment-53410002) entirely, so calm your worries. And if you are really insistent, you can find [plugins](https://github.com/hail2u/node-css-mqpacker) to run this transform for you against all logic.

# Comparison to Other Systems

There are a few other popular methodologies for writing CSS out there, but they all suffer from similar issues. We discussed at the beginning where the strengths of GPS are, and these are almost all where the weaknesses of other systems are. Most systems (SMACSS, BEM, OOCSS) do not use nesting at all. Instead of nesting, these systems propose that you name your selectors in a way that implies nesting, but then ignore the natural cascading of CSS entirely. For example, you might write normal CSS or GPS like this:

```css
#menu
  li
```

Whereas with BEM for example, it's all classes, and nesting means repeating the word in the class, like this:

```css
.menu
.menu__item
```

Not only is this gratuitous repetition, it's ignoring the natural power of CSS and the natural semantics of HTML, and is much more work to write and harder to read. And it gets worse and worse the more levels of nesting you have. Nesting in the DOM is an extremely effective way to control and organize your markup, why ignore this strength to favor a more repetitive and less clear strategy?

### Rules and Strict-ness

GPS is not the type of system that prescribes dogmas that can be followed robotically, and if you stay inside the rule set you are 100% set. It takes a lot of judgement and intuition to apply GPS correctly across any type of site. Categorizing pieces of the site correctly, writing clear and understandable selectors, not over-using classes and wrappers, and using descendant selectors correctly is not something that can be done elegantly with a 100% prescribed system, so you must use your best judgement with these pieces.

The primary aim of GPS is to prevent scope leaks. If you do an even half-decent job of following the rules, you will not run into any situation where you write styles that end up affecting another element somewhere else on your site without noticing. They key is starting out by writing your CSS at the section level, then pulling it up in scope as necessary. By default, you write CSS in the global scope, which is exactly the problem. GPS aims to reverse this by forcing you to write at the local scope by default, while also introducing some light organization to your codebase. If you do not follow this practice, you _will_ have scope leaks and will not get the benefits of GPS, so do try to be strict about that.

If you have questions and want to ask some other GPS experts, feel free to [join our gitter channel](https://gitter.im/jescalan/gps) and ask. But remember to slim down your example to the minimum possible code required in order to get the help you need. Ain't nobody got time for reviewing gigantic HTML and CSS files for free, so be specific with any issues you are struggling with.

### Try It!

GPS helps to create clear, readable, and modular HTML and CSS. Many other systems ignore the cascade and nesting and for that reason you have to do more work and repeat yourself. This is not the case with GPS, which takes advantage of the natural semantics of HTML and cascading properties of CSS to allow clean and quick styling. But the light structure it imposes also creates clear reuseable modules and safety from scope leaks. Give it a shot for your next project and see what you think!
