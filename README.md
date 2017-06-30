# Keep What You Learn
A reminder of what I've learned and will surely forgot

## Tasks List
- [ ] Add Markdown Infos (Regular&GitHub's ?)
- [ ] Add Dev tools' workspace linking information for less/sass/compiled/minified files
- [ ] Add Docker infos
- [ ] Check Service workers (App manifest/Offline management)
- [ ] SPIP - Link editorial objects : Check if there is no need of other files to make it work

----------------

## Summary
* [CSS](#css)
	* [Custom properties](#css-variables)
	* [CurrentColor](#css-currentcolor)
* [Javascript](#javascript)
	* [Exceptions](#javascript-exceptions)
	* [Strict mode](#javascript-strict)
	* [Accessor function](#javascript-accessor)
  * [Debugger](#javascript-debugger)
  * [Garbage Collection](#javascript-garbage-collection)
* [SPIP](#spip)
  * [Boucles](#spip-boucles)
  * [Filtres](#spip-filtres)
  * [Link editorial objects](#spip-link-objects)
* [SQL](#sql)
	* [Create stuff](#sql-create)
	* [Delete stuff](#sql-delete)
* [Node.js](#node)
	* [WTF](#node-wtf)
* [Git](#git)
	* [Branches](#git-branches)
	* [Check stuff](#git-check)
	* [Undo stuff](#git-reset)
	* [Rename stuff](#git-rename)
	* [Rebase stuff](#git-rebase)
* [Tools](#tools)
  * [Chrome Dev Tools](#chrome-dev-tool)
    * [Miscellaneous](#chrome-dev-tool-miscellaneous)
    * [Workspaces](#chrome-dev-tool-workspaces)
    * [Console](#chrome-dev-tool-console)
    * [Memory Management](#chrome-dev-tool-memory)
* [Accessibility](#accessibility)
 	* [Screen Readers](#accessibility-readers)
* [SEO](#seo)
	* [Microdata](#seo-microdata)
* [Others](#others)
	* [MySQL](#others-mysql)


<a id="css"></a>
## CSS

<a id="css-variables"></a>
### Custom properties
We can use css variables as we did with Less/Sass
More info 
- https://codepen.io/malyw/pen/rmBZee
- https://webkit.org/blog/5989/css-variables-in-webkit/

```css
:root{
 --main-color: #4d4e53;
 --main-bg: rgb(255, 255, 255);
 --header-height: 68px;
 --content-padding: 10px 20px;
 --font-size : 16px;
}

body {
	color : var(--main-color); // Output #4d4e53
	font-size: var(--font-size, 20px); // 20px is used as fallback if no --font-size defined
}
```

We can easily read/write it with Javascript.

```javascript
// READ
const rootStyles = getComputedStyle(document.documentElement);
const varValue = rootStyles.getPropertyValue('--screen-category').trim();

// WRITE
document.documentElement.style.setProperty('--screen-category', value);
```

<a id="css-currentcolor"></a>
### CurrentColor
Learn more : https://www.youtube.com/watch?v=krbKkLPXwlQ

`currentColor` can be use on a css property to make it inherit of the color of its parent.

```css
body {
	color : #444;
}

p {
	border-color: currentColor // Output is #444 because p's color value is inherited from body #444
}

```

<a id="javascript"></a>
## Javascript

<a id="javascript-exceptions"></a>
### Exceptions
- Create "Exception objects" to redefine Exception message.

```javascript

function getZipcode(value){
	if(// value is a zipcode){
		// Do something
	} else {
		throw new zipCodeException(value);
	}
}

function zipCodeException(value){
	this.value = value;
	this.message = " is not a valid zipcode";
	this.toString = function() { // Redefine toString to display better information than "[Object] Object"
		return this.value + this.message;
	}
}
```

<a id="javascript-strict"></a>
### Strict mode
Javascript can silently bypass code error then bugs may happen for "no reason" and difficult to debug. Strict mode shows those silent errors. We should use it systematically. See https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Strict_mode for more infos.
```javasrcipt
"use strict";
// At the begining of a file or juste before a function
// strict & non-strict code can't be concatenate
```

<a id="javascript-accessor"></a>
### Accessor function
* '+' can be used to force a value to integer like parseInt. It's faster but more restrictive.
```javascript
var number = "123"; // typeof number == "string"
var integer = +number; // typeof integer == "number"

/!\ Becarefull
var width = "30px";
parseInt(width) == 30;
+width == NaN; // because "30px" is not a number as a string
```

<a id="javascript-console"></a>
### Console
* console.warn : Output a string inside the console with a warning style.
* console.assert : Output the result of a true/false test. Ex: `console.assert(1 == 1)` ouptputs `true`.
* console.table : Output data into a table
  
<a id="javascript-garbage-collection"></a>
### Garbage Collection
 - If a variable is instanciated outside a Listener, that Listener uses in anyway (like checking it's type/value/...) and then makes that this variable is not usefull anymore (like this variable content was injected in the DOM but the Listener changes the DOM again), the variable won't be able to be garbage collected as it should because the reference of this variable still exists into the Listener.
 **TODO : Check veracity of what I'm talking about**

 ```javascript
 var input = $('<input id="newInput"/>'); //New DOM object
 $('form').html(input);
 window.addEventListener('click', function(e){
 	if(!input.hidden){ //Reference of the DOM object
		$('form').html('<a href="">...</a>'); //Change DOM
	}
 });
//The input object is no longer into the DOM but its reference inside a listener attached to the window make it enable to be garbage collected

 ```
 
<a id="spip"></a>
## SPIP

<a id="spip-boucles"></a>
### Boucles
- To get the siblings of a section/article, you have to use `{meme_parent}` as criteria of a loop. But this loop has to be inside a first loop that check the current section/article
```spip
<BOUCLE_siblings(RUBRIQUES){meme_parent}>
	//Result: NULL because #ID_RUBRIQUE (the idea of the current section)
</BOUCLE_siblings>

<BOUCLE_current(RUBRIQUES){id_rubrique}>
	<BOUCLE_siblings(RUBRIQUES){meme_parent}>
		//Result: List of siblings of current section
	</BOUCLE_siblings>
</BOUCLE_current>
```

- To display a loop only if a certain data exists, we can use a "fake" loop
```spip
<BOUCLE_fake(ARTICLES){0,1}>[(#ENV{data}|?{' '})]</BOUCLE_fake>
	// BOUCLE_data only occurs if the #ENV{data} exists because sets before </B_fake>
	<BOUCLE_data(ARTICLES){...}>
		// Stuff to get
	</BOUCLE_data>
</B_fake>
```

<a id="spip-filtres"></a>
### Filtres
- Use `|debug` to display information before a value compiled by spip.
```
[(#ID_ARTICLE|debug{id_article})] // Output: 'id_article' = X (where X is the ID_ARTICLE)
```

<a id="spip-link-objects"></a>
### Link editorial objects
To link an editorial objects 'stuff' to others, we need to create a specific table named '<prefix>stuff_liens' (default prefix is 'spip_') to stock the links between the objects. 
Then, we can displays the add/remove link bloc in the pages of the objects we want to link to 'stuff' using the `affiche_milieu` pipeline.

```php
// Display a add/remove bloc on ObjectToLinkWithStuff edition page to manage stuff links
if (!$e['edition'] and in_array($e['type'], array('ObjectToLinkWithStuff'))) {
	$texte .= recuperer_fond('prive/objets/editer/liens', array(
		'table_source' => 'stuff',
		'objet' => $e['type'],
		'id_objet' => $flux['args'][$e['id_table_objet']]
	));
}
```

*NOTE: It may need some files to create to make it work perfectly (ex. objets_lies_stuff.html/php...)*

<a id="sql"></a>
## SQL

<a id="sql-create"></a>
### Create stuff
- DataBase

```sql
CREATE DATABASE <database_name>;
```

- Table

```sql
CREATE TABLE <table_name>
(
<column_name> <column_type>, // Ex: id int,
<column_name> <column_type>, // Ex: title varchar(50),
<column_name> <column_type> // ...
);
```
	
- Column 

```sql
ALTER TABLE <table_name>
ADD COLUMN <column_name> <column_type>
```
<a id="sql-delete"></a>
### Delete stuff
- Database/Table
```sql
DROP DATABASE/TABLE <database/table_name>;
```

- Column 
```sql
ALTER TABLE <table_name>
REMOVE COLUMN <column_name>
```

<a id="node"></a>
## Node.js

<a id="node-wtf"></a>
### WTF
- Scripts inside preinstall/postinstall properties are automatically executed during a `node run` before/after installing the dependencies. And for all the packages.

We can prevent that with a `npm install --ignore-scripts` but it can deny good installation.
Otherwise, check for preinstall/postinstall in all dependencies package...

<a id="git"></a>
## Git

:warning: ALWAYS BEWARE OF `--hard` !

<a id="git-branches"></a>
### Branches
- Checkout a remote branch with a name beginning by '#', '-'...
```
git checkout -b easy-name origin\#strange-name //New branch has same name but no special character and is linked to remote branch with strange name
```

<a id="git-check"></a>
### Check stuff
- See what is going to be pushed

```
git diff --stat --cached origin/master
// Output ex : article.html++++----

git diff origin/dev // Diff between local files pushed and remote files
// Output ex : 
<a href="#URL_SITE_SPIP" title="Aller à l'accueil"><span>#GET{titre}</span>
	<svg version="1.1" class="panda original pink" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px" viewBox="0 0 800 800" xml:space="preserve">
	   <polygon class="ctr" points="0,0 0,600 200,800 200,700 600,700 600,800 800,600 800,0" />
-                          <path class="org" class="or" d="M0,0 0,200 200,0 z" />
-                          <path class="ord" class="or" d="M800,0 800,200 600,0 z" />
+                          <path class="org or" d="M0,0 0,200 200,0 z" />
+                          <path class="ord or" d="M800,0 800,200 600,0 z" />
	   <path class="nz" d="M300,400 500,400 400,500 z" />
	   <path class="bo" d="M300,500 500,500 400,600 z" />
-                          <polygon class="og" class="oy" points="400,200 200,200 100,300 100,400 200,500 200,400" />
-                          <polygon class="od" class="oy" points="400,200 600,200 700,300 700,400 600,500 600,400" />
+                          <polygon class="og oy" points="400,200 200,200 100,300 100,400 200,500 200,400" />
+                          <polygon class="od oy" points="400,200 600,200 700,300 700,400 600,500 600,400" />
	   <circle cx="200" cy="300" r="25" />
	   <circle cx="600" cy="300" r="25" />
	</svg>
</a>
-       </li>
 ]
```

<a id="git-reset"></a>
### Undo stuff
- Reset a rebase

To reset a rebase on a current branch, use `reflog` to find the head of the current branch right before the rebase. Then `git reset --hard` to it.

```
git reflog
dadb174 HEAD@{1}: rebase finished: returning to refs/heads/dev
dadb174 HEAD@{2}: rebase: #2139 @1h : Remove rubrique-project hardocode by real articles list
758481d HEAD@{3}: pull --rebase -v: checkout 758481d9ec15dce1eded33299534082b08a53a33
fd5c427 HEAD@{4}: commit: #2139 @1h : Remove rubrique-project hardocode by real articles list
c1ad5a2 HEAD@{5}: commit: #2139 @0.5h : Factorize projects-grid-row files + Update their classes + Delete useless files

The HEAD before the rebase was HEAD@{4} then just

git reset --hard HEAD@{4}
```

- Reset commits

```
git reset --hard 0d1d7fc32 // Reset all commits and file changes since 0d1d7fc32 :warning: Files modifications too !

git reset --soft 0d1d7fc32 // Reset all commits but keep track of file modification. They can be committed again.
```

<a id="git-rename"></a>
### Rename stuff
- Rename a branch

```
git branch -m <old-name> <new-name> //If on another branch
git branch -m <old-name> //If on the branch to rename
```

<a id="git-rebase"></a>
### Rebase stuff
- Merge commits

If we want to merge multiple commits into a single one.
```
git rebase -i <commit-SHA> 

// Ex: output
pick 01d1124 Adding license
pick 6340aaa Moving license into its own file
pick ebfd367 Jekyll has become self-aware.
pick 30e0ccb Changed the tagline in the binary, too.

// Pick last commit and squash others with it then save changes
pick 01d1124 Adding license
squash 6340aaa Moving license into its own file
squash ebfd367 Jekyll has become self-aware.
squash 30e0ccb Changed the tagline in the binary, too.

// Ex. output
# This is a combination of 4 commits.
# The first commit's message is:
Adding license

# This is the 2nd commit message:

Moving license into its own file

...

// Delete all  commits messages and add the one you want for the commit to be created then save changes 
// New commit:
Created commit 0fc4eea: Creating license file, and making jekyll self-aware.
```

<a id="tools"></a>
## Tools

<a id="chrome-dev-tool"></a>
### Chrome Dev Tools (v.56.0.2924.87)
Stuff Learned from [CodeSchool](http://discover-devtools.codeschool.com)

<a id="chrome-dev-tool-miscellaneous"></a>
#### Miscellaneous
* Shortcut to open/close Chrome Dev Tools: Cmd(⌘) + Alt(⌥) + I
* Shortcut to access parameters: Cmd(⌘) + Shift(⇧) + P
* We can see/delete page cookies in *Application/Storage/Cookies*
* We can enable/disable Chrome foncionalities in the page `chrome://flags`
	* We can enable Experimentals canvas feature to add more file linking tools to Chrome Dev Tools

<a id="chrome-dev-tool-workspaces"></a>
#### Workspaces
We can link local files to dev tools source files by drag&dropping a local dir into the Source panel.
* With 'Experimentals canvas feature' activated, linking is almost automatic
* With 'Quirk mode' activated (with Cmd(⌘) + Shift(⇧) + P), selecting an css attribute (for ex.) highlights it in the local file directly in the Quirk mode panel so we can edit it directly.

<a id="chrome-dev-tool-debugger"></a>
#### Debugger
* $('expression') selects all page elements that matche 'expression' (like in jQuery). The output is slightly different than jQuery's $('') and jQuery overrides chrome dev tools' behaviour.
* `$0` will select the last element you've inspected. `$1` highlights the second-to-last and so on.
* `inspect($('expression'))` will inspect the element that matches 'expression'. 
* 'Pause on Exceptions' button (rounded shape with two || inside the debugger panel) can be used to block script when an exception appears.
* Local Storage data (cookies,...) can be edited inside the *Application/Storage/LocalStorage* list.
* In responsive mode
 * Use ⋮ icon to display more informations (rulers, network throttling...)
 * Use "Nexus 5X" to get more Screen options (with keyboards, with adress bar...)
* We can connect an androïd device and got to "Show Remote Devices", enable port forwarding with a port number and then launch a `localhost:<port>` on the device to debug it in real time

<a id="chrome-dev-tool-memory"></a>
#### Memory Management
* Heap Snapshots

 You can take Heap Snapshots to see every objects loaded into a page. Then you can take an other one after making some interations in the page and use "Comparison" to see difference between the two collections.
 After that, red "Detached DOM tree" show us wich html elements haven't been garbage collected (i.e. the memory used for the element is still used although the element doesn't exists anymore).
 
<a href="accessibility"></a>
## Accessibility

<a href="accessibility-readers"></a>
### Screen Readers
On MacOS, we can use [VoiceOver](http://webaim.org/articles/voiceover/) as screen reader. 
 
 <a href="seo"></a>
 ## SEO

<a href="seo-microdata"></a>
### Microdata

:warning: Use absolute url for page & img references.

- [Google Knowledge Graph](https://developers.google.com/search/docs/data-types/sitename)

Used to display a chosen name & logo in google search results + tell Google wich social media pages are linked to the website.

```javascript
<!-- Knowledge Graph Google -->
<script type="application/ld+json">
{
	"@context": "http://schema.org",
	"@type": "WebSite",
	"name": "{Website's name}",
	"url": "{Website's URL}"
}
</script>
<script type="application/ld+json">
{ 
	"@context" : "http://schema.org",
	"@type" : "Organization",
	"name": "{Organization's name}",
	"url": "{Organization website URL}",
	"logo": "{Organization logo's URL}",
	"sameAs" : [ 
		"https://www.facebook.com/YourPage/",
		"https://www.linkedin.com/company/YourOrganization",
		"https://twitter.com/yourAccount",
		...
	] 
}
</script>
<!-- End Knowledge Graph Google -->
```

- [Google Breadcrumb](https://developers.google.com/search/docs/data-types/breadcrumbs#guidelines)

Set in all pages but homepage, this snippet shows the breadcrumb of the current page. It must shown a list of subnavigation items (i.e sections and subsections).

```javascript
<script type="application/ld+json">
{
 "@context": "http://schema.org",
 "@type": "BreadcrumbList",
 "itemListElement":
 [
  {
   "@type": "ListItem",
   "position": 1,
   "item":
   {
    "@id": "{URL Section1}",
    "name": "{Name Section1}"
    }
  },
  {
   "@type": "ListItem",
   "position": 2,
   "item":
   {
    "@id": "{URL Section2}",
    "name": "{Name Section2}"
    }
  },
  ...,
  {
   "@type": "ListItem",
  "position": x,
  "item":
   {
     "@id": "{URL current page}",
     "name": "{Name current page}"
   }
  }
 ]
}
</script>
```
- [Google SearchBox](https://developers.google.com/search/docs/guides/enhance-site#troubleshooting_your_markup)

This snippet placed in the homepage's `head` forces google to use the website search engine instead of default google search. The searchbox is displayed on a search results page for a brand name (ex: Pinterest or pinterest.com).

```javascript
<script type="application/ld+json">
{
  "@context": "http://schema.org",
  "@type": "WebSite",
  "url": "{Site URL}",
  "potentialAction": {
    "@type": "SearchAction",
    "target": "{Site search page URL}?q={Placeholder for the google searchbar}",
    "query-input": "required name={Same placeholder than before}"
  }
}
```

To make sure google doesn't display any searchbox, insert `<meta name="google" content="nositelinkssearchbox" />` in `head`.

- Microformat Site Navigation

To control the links displayed on a Google search result page, we can use Microformat Site navigation on main navigation items.
More info : https://schema.org/SiteNavigationElement

```
<ul itemscope itemtype="http://www.schema.org/SiteNavigationElement">
  <li itemprop="name"><a itemprop="url" href="{url 1}">Link 1</a></li>
  <li itemprop="name"><a itemprop="url" href="{url 2}">Link 2</a></li>
  <li itemprop="name"><a itemprop="url" href="{url 3}">Link 3</a></li>
</ul>
```

- [Microformat NewsArticle](https://developers.google.com/structured-data/rich-snippets/articles#article_markup_properties)

This snippet makes Google to better understand the semantic purpose of the news/article page.
More infos :
https://developers.google.com/structured-data/rich-snippets/articles#article_markup_properties
https://developers.google.com/structured-data/carousels/top-stories 
https://developers.google.com/search/docs/data-types/articles#amp-logo-guidelines (Logo sizing)

```javascript
<script type="application/ld+json">
{
    "@context": "http://schema.org",
    "@type": "NewsArticle",
    "mainEntityOfPage":{
      "@type":"WebPage",
      "@id":"{Site URL}"
    },
    "headline": "{Article Title}",
    "alternativeHeadline": "{Article headline}",
    "description": "{Article short description or first lines}",
    "image": {
      "@type": "ImageObject",
      "url": "{Article main visual}",
      "height": {Article main visual height}, // in pixels
      "width": {Article main visual width} // in pixels
    },
    "datePublished": "2011-03-21T14:30:09+00:00", //W3C format
    "dateModified": "2014-09-15T14:59:48+00:00", //W3C format
    "keywords":["{keyword 1}","{keyword 2}",...], 
    "articleSection":"{Parent section's name}",
    "author": {
      "@type": "Person",
      "name": "{Site name}"
     },
     "publisher": {
     "@type": "Organization",
     "name": "{Site name}",
     "logo": {
        "@type": "ImageObject",
        "url": "{Organisation logotype URL}",
        "width": {Organisation logotype width}, // in pixels
        "height": {Organisation logotype height} // in pixels
     }
   }
}
```
- [Microformat Video](https://developers.google.com/search/docs/data-types/videos)

This snippet comes in addition with NewsArticle Microformat when a video exists on the page.

```javascript
<script type="application/ld+json">
{
  "@context": "http://schema.org",
  "@type": "VideoObject",
  "name": "{Video title}",
  "description": "{Vide short descritpion}",
  "thumbnailUrl": "{Video thumbnail URL}",
  "uploadDate": "2015-02-05T08:00:00+08:00", // W3C format
  "duration": "PT1M33S", // Video duration in ISO 8601 format
  "publisher": {
    "@type": "Organization",
    "name": "{Organization name}",
    "logo": {
      "@type": "ImageObject",
      "url": "{Organization logo URL}",
      "width": {Organization logo URL width}, // in pixels
      "height": {Organization logo URL height} // in pixels
    }
  },
  "contentUrl": "{Video URL}",
  "embedUrl": "{Page URL}",
  "interactionCount": "{Number of Views}"
}
</script>
```

- Twitter Cards

Twitter uses specific `<meta>` to build a card when an url is shared. We can use *[Summary](https://dev.twitter.com/cards/types/summary)* on every pages and *[Summary with large image](https://dev.twitter.com/cards/types/summary-large-image)* pages with nice picture.
Validator : https://cards-dev.twitter.com/validator 

```
<!-- Twitter Card Summary -->
<meta name="twitter:card" content="summary" />
<meta name="twitter:site" content="@{Twitter account}" />
<meta name="twitter:creator" content="@{Twitter account}" />
<meta name="twitter:domain" content="{Organization name}" />
<meta name="twitter:title" content="{Page title}" />
<meta name="twitter:description" content="{Page description}" />
<meta name="twitter:image:src" content="{Main picture URL}" />
<meta name="twitter:url" content="{Page URL}" />

<!-- Twitter Card Summary with large image -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:site" content="@{Twitter account}">
<meta name="twitter:creator" content="@{Twitter account}">
<meta name="twitter:domain" content="{Organization name}" />
<meta name="twitter:title" content="{Page title}">
<meta name="twitter:description" content="{Page description}">
<meta name="twitter:image" content="{Main picture URL}">
<meta name="twitter:url" content="{Page URL}" />
```

- Opengraph

Many social networks or sharing services uses opengraph to display informations from shared url. This snippet must exists on every pages but there is specific parts (see below).
Validator : https://developers.facebook.com/tools/debug/

For facebook, we must get an `app_id` by creating a facebook app. More info https://developers.facebook.com/docs/apps/register 

```
// Generic part
<!-- Open Graph -->
<meta property="fb:app_id" content="{Facebook App ID}/> 
<meta property="og:locale" content="fr_FR" />
<meta property="og:site_name" content="{Site name}" />
<meta property="og:url" content="{Page URL}" />
<meta property="og:type" content="article" />
<meta property="og:title" content="{Page title}" />
<meta property="og:description" content="{Page or Site description}" />

// If the page is a section or an article with a Main picture
<meta property="og:image" content="{Main image URL}" /> // 1200x630px
<meta property="og:image:width" content="{Main image width}" /> // 1200 or less
<meta property="og:image:height" content="{Main image height}" /> // 630 or less

// If the page has not a Main picture
<meta property="og:image" content="{Default image URL}" // 1200x630px
/>
<meta property="og:image:width" content="{Default image width}" />
<meta property="og:image:height" content="{Default image height}" />

// If the page is an article 
<meta property="article:publisher" content="{Facebook page URL}" />
<meta property="article:section" content="{Parent section name}" />
<meta property="article:published_time" content="2016-04-11T08:00:00+08:00" /> // W3C format
```

- Favicon

A favicon.ico must exists in the site's root directory because it's the one used for non HTML requests (ex: request to an image or en pdf).

Link to the web favicon (16x16px)
`<link rel="icon" href="http://www.xxx.fr/favicon.gif" type="image/gif">`

Others favicons for apple devices
```
<link rel="apple-touch-icon" sizes="76x76" href="{76x76 icon URL}">
<link rel="apple-touch-icon" sizes="120x120" href="{120x120 icon URL}">
<link rel="apple-touch-icon" sizes="152x152" href="{152x152 icon URL}">
<link rel="apple-touch-icon" sizes="167x167" href="{167x167 icon URL}">
<link rel="apple-touch-icon" sizes="180x180" href="{180x180 icon URL}">
```

<a id="others"></a>
## Others
<a id="others-mysql"></a>
### MySQL
* Solve local problem of ".pid file not found"

It must be a permission issue
```
ls -laF /usr/local/var/mysql/ //Check the ownership of mysql folder

sudo chown -R mysql /usr/local/var/mysql/ //Change ownership to mysql
```
