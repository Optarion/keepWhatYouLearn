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
* [Tools](#tools)
  * [Chrome Dev Tools](#chrome-dev-tool)
    * [Miscellaneous](#chrome-dev-tool-miscellaneous)
    * [Console](#chrome-dev-tool-console)
    * [Memory Management](#chrome-dev-tool-memory)


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
 **TODO : Update code example**
 ```javascript
 var input = '<input id="newInput"/>';
 window.addEventListener('click', function(e){
  
 })
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
<a id="tools"></a>
## Tools

<a id="chrome-dev-tool"></a>
### Chrome Dev Tools (v.56.0.2924.87)
Stuff Learned from [CodeSchool](http://discover-devtools.codeschool.com)

<a id="chrome-dev-tool-miscellaneous"></a>
#### Miscellaneous
- Shortcut to open/close Chrome Dev Tools: Cmd + Alt + I

<a id="chrome-dev-tool-debugger"></a>
#### Debugger
- $('expression') selects all page elements that matche 'expression' (like in jQuery). The output is slightly different than jQuery's $('') and jQuery overrides chrome dev tools' behaviour.
- `$0` will select the last element you've inspected. `$1` highlights the second-to-last and so on.
- `inspect($('expression'))` will inspect the element that matches 'expression'. 
- 'Pause on Exceptions' button (rounded shape with two || inside the debugger panel) can be used to block script when an exception appears.
- LocalStorage data can be edited inside the *Application/Storage/LocalStorage* list.

<a id="chrome-dev-tool-memory"></a>
#### Memory Management
- Heap Snapshots
 You can take Heap Snapshots to see every objects loaded into a page. Then you can take an other one after making some interations in the page and use "Comparison" to see difference between the two collections.
 After that, red "Detached DOM tree" show us wich html elements haven't been garbage collected (i.e. the memory used for the element is still used although the element doesn't exists anymore).
