# Keep What You Learn
A reminder of what I've learned and will surely forgot

## Tasks List
- [ ] Add Markdown Infos (Regular&GitHub's ?)

----------------

## Summary
* [Javascript](#javascript)
  * [Debugger](#javascript-debugger)
  * [Garbage Collection](#javascript-garbage-collection)
* [SPIP](#spip)
  * [Boucles](#spip-boucles)
* [Git](#git)
	* [Undo stuff](#git-reset)
	* [Rename stuff](#git-rename)
* [Tools](#tools)
  * [Chrome Dev Tools](#chrome-dev-tool)
    * [Miscellaneous](#chrome-dev-tool-miscellaneous)
    * [Console](#chrome-dev-tool-console)
    * [Memory Management](#chrome-dev-tool-memory)
* [SEO](#seo)
	* [Microdata](#seo-microdata)
* [Others](#others)
	* [MySQL](#others-mysql)


<a id="javascript"></a>
## Javascript

<a id="javascript-console"></a>
### Console
  - console.warn
  Output a string inside the console with a warning style.
  - console.assert
  Output the result of a true/false test. Ex: `console.assert(1 == 1)` ouptputs `true`.
  
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
<a id="git"></a>
## Git

ALWAYS BEWARE OF `--hard` !

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
<a id="git-rename"></a>
### Rename stuff
- Rename a branch

```
git branch -m <old-name> <new-name> //If on another branch
git branch -m <old-name> //If on the branch to rename
```
<a id="tools"></a>
## Tools

<a id="chrome-dev-tool"></a>
### Chrome Dev Tools (v.56.0.2924.87)
Stuff Learned from [CodeSchool](http://discover-devtools.codeschool.com)

<a id="chrome-dev-tool-miscellaneous"></a>
#### Miscellaneous
- Shortcut to open/close Chrome Dev Tools: Cmd(⌘) + Alt(⌥) + I
- You can see/delete page cookies in *Application/Storage/Cookies*

<a id="chrome-dev-tool-debugger"></a>
#### Debugger
* $('expression') selects all page elements that matche 'expression' (like in jQuery). The output is slightly different than jQuery's $('') and jQuery overrides chrome dev tools' behaviour.
* `$0` will select the last element you've inspected. `$1` highlights the second-to-last and so on.
* `inspect($('expression'))` will inspect the element that matches 'expression'. 
* 'Pause on Exceptions' button (rounded shape with two || inside the debugger panel) can be used to block script when an exception appears.
* LocalStorage data can be edited inside the *Application/Storage/LocalStorage* list.

<a id="chrome-dev-tool-memory"></a>
#### Memory Management
* Heap Snapshots

 You can take Heap Snapshots to see every objects loaded into a page. Then you can take an other one after making some interations in the page and use "Comparison" to see difference between the two collections.
 After that, red "Detached DOM tree" show us wich html elements haven't been garbage collected (i.e. the memory used for the element is still used although the element doesn't exists anymore).
 
 <a href="seo"></a>
 ## SEO

<a href="seo-microdata"></a>
### Microdata
- Google Knowledge Graph https://developers.google.com/search/docs/data-types/sitename

Used to display a chosen name & logo in google search results + tell Google wich social media pages are linked to the website.
:warning: Use absolute url for page & img references

```javascript
<!-- Knowledge Graph Google -->
<script type="application/ld+json">
{
	"@context": "http://schema.org",
	"@type": "WebSite",
	"name": "Website's name",
	"url": "Website's URL"
}
</script>
<script type="application/ld+json">
{ 
	"@context" : "http://schema.org",
	"@type" : "Organization",
	"name": "Organization's name",
	"url": "Organization website URL",
	"logo": "Organization's logo",
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
<a id="others"></a>
## Others
<a id="others-mysql"></a>
### MySQL
* Solve local problem of ".pid file not found"

It must be a permission issue
```
ls -laF /usr/local/var/mysql/ /usr/local/var/mysql/ //Check the ownership of mysql folder

sudo chown -R mysql //Change owneship to mysql
```
