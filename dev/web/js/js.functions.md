---
title: JS functions
---

MISC
==========

TOC
------------

#### getSubheaders()

```js
function getSubHeaders(element, wantedHeaders = 'H1,H2,H3,H4,H5') {
    // V6
    
    // check if allHeaders array exists, built it if not:
    if (typeof allHeaders == 'undefined') {let allHeaders;}
    if (!allHeaders) { 
        allHeaders = getAllHeaders(wantedHeaders)
        console("allHeaders created")
    }
    // get lvl:    
    let startLvl = Number(element.tagName[1]);
    // get index of array where selected header shows up:
    let index = allHeaders.findIndex( item => item == element)
    console.log(`starting with index ${index} and level ${startLvl}`)
    // get all sub headers that come after the selected one until one of the same level ist reached:
    let currentLvl;
	let subHeaders = [];
    index++;
    for (; index < allHeaders.length; index++) {
	    currentLvl = allHeaders[index].tagName;
		currentLvl = Number(currentLvl[1])
		console.log(`Index set to: ${index} --> Sub header found: ${allHeaders[index].tagName} --> CurrentLvl: ${currentLvl}`)
		if (currentLvl <= startLvl) {
			console.log("No sub headers left")
			break;
		}
		if (currentLvl == startLvl+1) {
			console.log("Next level sub found!")
            subHeaders.push(allHeaders[index]) 
        }
    }
	return subHeaders;
}
```

EVENTS
============

EventListeners
----------------

```js
// PROBLEM: We reference the button in the body from the JS in the head.
// SOLUTION: Wrap it in another Event Listener which is waiting for DOMContentLoaded.
    document.addEventListener('DOMContentLoaded', () => {
        let button = document.getElementById('btn');
        button.addEventListener('click', () => {
            // handle the click event
        });
    });
```

DOM
==================


#### createElement()
```js
function createElement (elementType="div", content) { 
    var newElement = document.createElement(elementType); 
    var newContent = document.createTextNode(content); 
    newElement.appendChild(newContent);  
    // add the newly created element and its content into the DOM: 
    return newElement;
}
// usage:
document.body.append(createElement("article", "This is my article!"))
```

#### addContent()
```js
function addContentToId(elementType='p', content, destination='id') {
	console.log('addContent called')
	let newElement = document.createElement(elementType);
	let newContent = document.createTextNode(content);
	newElement.appendChild(newContent);
	document.getElementById(destination).appendChild(newElement);
}
// usage:
addContent("article", "This is my article!", "main-content"))
```
#### insertAfter()
```js
	function insertAfter(nodeList, position) {
		// nodeList.reverse();
		nodeList.forEach( item => {
			position.parentNode.insertBefore(item, position.nextSibling);
		});
	}
```
####
```js
function getAllHeaders(headers) {
    return Array.from(document.querySelectorAll(headers))
}
```

Media
==============

### Audio

```js
// needs Howler
function clickToPlay(audioSrc) {
	let audio = new Howl({
		src: audioSrc
	})
	audio.play()
}
```

Animation
=================


```js
function animationLR(animElement, animContainerId) {
	let elem = animElement;
	let widthContainer = document.getElementById(animContainerId).offsetWidth;
	let widthFinal = widthContainer - elem.offsetWidth
	let pos = 0;
	let phase;
	var id = setInterval(timeHandler, 5);

	function timeHandler() {
		// phase switch
		if (pos == widthFinal) { phase = 'toLeft' }
		if (pos == 0) { phase = 'toRight' }
		// move
		if (phase == 'toRight') {
			pos++;
			elem.style.left = pos + 'px';
		}
		if (phase == 'toLeft') {
			pos--;
			elem.style.left = pos + 'px';
		}
	}
	
	// can-'t stop it yet
	function stopAnimation() {
		clearInterval(id);
	}
}
```