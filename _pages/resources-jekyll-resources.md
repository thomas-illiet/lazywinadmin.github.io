---
layout: archive
title: "Jekyll Resources"
permalink: /resources/jekyll/resources
author_profile: false
sidebar:
  nav: "resources"
---

# Markdown

## Template/Manipulation/Expand etc..

https://jekyllrb.com/docs/templates/

## Links

https://jekyllrb.com/docs/templates/#linking-to-posts

### Variable
Declare:`[Link1]: http://lazywinadmin.com`
Use it: `[Some Text][Link1]`

or

Declare:`[Link1]: http://lazywinadmin.com "Some description here"`
Use it: `[Some Text][Link1]`

the last part `"Some description here"` will be visible when you pass over the link

### Image
Declare:`[ImageLink1]: http://lazywinadmin.com/lazy.jpg`
Use it: `![Description][ImageLink1]`

You can also add a text that will be visible when you pass over.
Declare:`[ImageLink1]: http://lazywinadmin.com/lazy.jpg "Some details"`
Use it: `![Description][ImageLink1]`

or

Declare:`[ImageLink1]: http://lazywinadmin.com/lazy.jpg "Some details"`
Use it: `![Description][ImageLink1]`

### Link
`<http://lazywinadmin.com>`
or
`[http://lazywinadmin.com](http://lazywinadmin.com)`


### Emoticons

Just copy and paste those: [https://emojipedia.org/](https://emojipedia.org/)

### Update the top right navigation

`navigation.yml`

### Build a navigation menu for a series of posts

`navigation.yml`
You will also need to specify in the top of your post the navigation info

### Edit the profile picture

Update `img {` in `_sidebar.scss`

```css
.author__avatar {
  display: table-cell;
  vertical-align: top;
  width: 36px;
  height: 36px;

  @include breakpoint($large) {
    display: block;
    width: auto;
    height: auto;
  }

  img {
    max-width: 110px;
    border-radius: 50%;

    @include breakpoint($large) {
      padding: 5px;
      border: 1px solid $border-color;
    }
  }
}
```


### Using the .notice from minimal mistkaes

My text {.notice}

My text {.notice--warning}

{% capture mynote%}
My text
{% endcapture %}

{{mynote}}{: .notice--warning}


{% capture notice-text %}
You can also add the `.notice` class to a `<div>` element.

* Bullet point 1
* Bullet point 2
{% endcapture %}

<div class="notice--info">
  <h4>Notice Headline:</h4>
  {{ notice-text | markdownify }}
</div>

### Landing Page Header/Overlay

Edit the index.html page at the root of the repo.

examepl:

excerpt: "PowerShell, Automation, Windows Server and Much more .. 🚀"
header:
  overlay_image: /images/headers/mountain01_1920x500.jpg

### Headers

```
=
```


### Line

```
---
```

### Bold, Underline, Italic

### Quote
```
> My Paragraph yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada
```
> My Paragraph yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada yada

### Lists

```
- Item
- Item
- Item
```
- Item
- Item
- Item


```
1. Item
1. Item
1. Item
```
1. Item
1. Item
1. Item

## Cheat sheet
https://learn.cloudcannon.com/jekyll-cheat-sheet/

## Liquid
http://www.rubydoc.info/github/Shopify/liquid/Liquid
https://github.com/Shopify/liquid/wiki/Liquid-for-Designers

### date format

http://alanwsmith.com/jekyll-liquid-date-formatting-examples

### Include another page

You can include the content of another file using this:
```
{% include read-time.html %}
```
Source: http://www.rubydoc.info/github/Shopify/liquid/Liquid/Include




## Icons

http://www.flaticon.com/
https://thenounproject.com/