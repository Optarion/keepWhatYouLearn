# Keep What You Learn
A reminder of what I've learned and will surely forgot

 * [Javascript](#javascript)
  * [Console](#javascript-console)
  * [Garbage Collection](#javascript-garbage-collection)
* [Tools](#tools)
 * [Chrome Dev Tools](#chrome-dev-tool)
  * [Miscellaneous](#chrome-dev-tool-miscellaneous)
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

<a id="tools"></a>
## Tools

<a id="chrome-dev-tool"></a>
### Chrome Dev Tools
Stuff Learned from [discover-devtools.codeschool.com]

<a id="chrome-dev-tool-miscellaneous"></a>
#### Miscellaneous
- Shortcut to open/close Chrome Dev Tools: Cmd + Alt + I

<a id="chrome-dev-tool-memory"></a>
#### Memory Management
- Heap Snapshots
 You can take Heap Snapshots to see every objects loaded into a page. Then you can take an other one after making some interations in the page and use "Comparison" to see difference between the two collections.
 After that, red "Detached DOM tree" show us wich html elements haven't been garbage collected (i.e. the memory used for the element is still used although the element doesn't exists anymore).
