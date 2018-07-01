# CSS Notes：Ways to Kill the Spaces in Horizontal Layout

@(HTML&CSS)

***
There are many ways to kill the spaces. Some funky ways like minimized HTML and skipping the closing tag will also be mentioned.

Note that many of these methods is using the ``inline-block`` property, which is only of use on [inline elements](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements).

### 1. Set the Font Size to Zero
 * Setting a ``font-size`` of 0 on the container itself (thus causing a blank space character to have zero width), and then resetting the size on the items.

 ```CSS
     .navbar ul {
	 /*set the font-size to 0*/
         font-size: 0;
     }
     .navbar li {
         /*reset the font-size*/
         font-size: 16px;
         font-size: 1rem;
     }
```
#### 1.2 Drawbacks

1. In Android, the way mentioned above become no use at all. [Reseach](https://codepen.io/stowball/details/LsICH)
2. Be careful about  using ``em ``or`` %``,  because they cascade the children would also have zero font size.
3. If you use ``@font-face`` with this technique in Safari 5.0.x, the fonts will lose anti-aliasing. (If you set the font-size above 4px, it will clear the bug)
[Screenshot]( https://cdn.css-tricks.com/wp-content/uploads/2012/04/Screen-Shot-2013-06-11-at-4.23.03-PM.png)  
[Testcase](http://jsfiddle.net/39GZd/7/)

### 2. Negative Margin
Setting negative margin is also useful to scoot the elements back. The font-size property may need to be adjusted based on font size of parent.

```CSS
	nav a {
	    display: inline-block;
	    margin-right: -4px;
	}
```

#### 2.1 Drawbacks

This technique is problematic in IE 6 & 7. Also, the display breaks in other browsers when I run the pen in CodePen.

[Screenshot in Firefox 61.0](https://s15.postimg.cc/95ei4lni3/2018-07-01_7.17.00.png)
[Screenshot in Chrome 67.0.3396](https://postimg.cc/image/6o2qxm693/)
[Screenshot in Safari 11.1](https://s15.postimg.cc/4xjpw0tu3/2018-07-01_7.28.09.png)

When I set the margin-right less than -4 (like -5 or -6), it will clear the bug but the width of the container is smaller than it usually is. It quite relies on size assumption, and the display differs in different browsers.

### 3. Reset the Font-family Property

Using [a tiny custom font](https://matthewlein.com/articles/inline-block-fix), only containing a blank space character with a zero width on the parent element. The original ``font-family`` is then reset on the child elements. 

It works with any em-based elements, and does not rely on size assumptions.

```CSS
	nav ul {
	    /*clear the font-family property*/
	    font-family: empty;
	    margin: 0;
	    padding: 0;
	    text-align: center;
	}
	nav li {
	    /*reset the font-family property*/
	    font-family: serif;
	    display: inline-block;
	    background-color: #CCC;
	    vertical-align: top;
	    /*fix the display problem in Firefox*/
	    padding: 0 1px;
	}
```

#### 3.1 Drawbacks
1. It does not work on IE8 and the content is a bit closer in Firefox;
2. Some extra k to download base64 font, ttf is needed for older android and svg for several versions old iOS;
3. There is a tiny flash as it loads.

### 4. Float
You can use the float property instead of using inline-block, but you cannot expect what it finally comes out.

### 5. Minimized HTML

 ```html
	<ul>
	    <li>
	    one</li><li>
	    two</li><li>
	    three</li>
	</ul>

or

```html
	<ul>
	    <li>one</li
	    ><li>two</li
	    ><li>three</li>
	</ul>
```

or with comments:

```html
	<ul>
	    <li>one</li><!--
	    --><li>two</li><!--
	    --><li>three</li>
	</ul>
```


It is quite messy and hard to maintain although it does the trick. I am not a big fun of this technique anyway \\( ツ)/

### 6. Skip the Closing Tag

```html
	<ul>
	    <li>one
	    <li>two
	    <li>three
	</ul>
```

### 5. Flexbox

```CSS
	ul {
	    display: -webkit-box;/* OLD - iOS 6-, Safari 3.1-6 */
	    display: -moz-box; /* OLD - Firefox 19- (buggy but mostly works) */
	    display: -ms-flexbox; /* TWEENER - IE 10 */
	    display: -webkit-flex; /* NEW - Chrome */
	    display: flex; /* NEW, Spec - Opera 12.1, Firefox 20+ */
	}
```


#### 5.1 Drawback
It does not work on Opera below 12.1 and IE below 10.


### 6. Letter-spacing and Word-spacing on Grid Container

This technique is used in Yahoo's [YUI 3 CSS Grids](http://yui.yahooapis.com/3.5.0/build/cssgrids/grids.css). I have no experience of YUI, maybe it worths a good try.

```CSS
	.yui3-g {
	    letter-spacing: -0.31em; /* webkit */
	    *letter-spacing: normal; /* reset IE < 8 */
	    word-spacing: -0.43em; /* IE < 8 && gecko */
	}

	.yui3-u {
	    display: inline-block;
	    zoom: 1; *display: inline; /* IE < 8: fake inline-block */
	    letter-spacing: normal;
	    word-spacing: normal;
	    vertical-align: top;
	}
```

#### 6.1 Drawbacks

I tested the letter-spacing property and word-spacing property respectively, and the result of setting letter-spacing turned out to be really weird no matter in Firefox, Chrome or Safari: [Screenshot](https://s15.postimg.cc/ufbb79inv/2018-07-01_9.25.26.png)

The final result of testing word-spacing property is like the negative margin method, which is quite dependent on size assumptions.

## See Also

1. A Complete Guide to Flexbox, Chris Coyier: https://css-tricks.com/snippets/css/a-guide-to-flexbox/
2.  MDN web docs - Inline elements: https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements
3.  Using Flexbox: Mixing Old and New for the Best Browser Support, Chris Coyier: https://css-tricks.com/using-flexbox/
4.  Ways to handle spaces between inline-block elements, Chris Coyier: https://codepen.io/chriscoyier/pen/hmlqF


## References

1. Another way to kill space between inline-blocks, Matthew Lein: https://matthewlein.com/articles/inline-block-fix
2. CSS Mastery (Third Edition), Andy Budd & Emil Bjorklund, Chapter 6
3. Fighting the Space Between Inline Block Elements, Chris Coyier: https://css-tricks.com/fighting-the-space-between-inline-block-elements/ 
4. YUI 3 CSS Grids: http://yui.yahooapis.com/3.5.0/build/cssgrids/grids.css
