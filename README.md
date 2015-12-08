# Maintaining Scroll Position between page load

Base on slideshow or tab functionality (how generally they function) within a website, macys.com marketting team wanted a sitelet that have multiple contents but want each content to be on a seperate page with titles and metadata. Normally, when you scroll down a web page and click on a link, the scroll bar jumps back to the top when the new page is loaded.

Every once in a while, you might find yourself in a scenario where it would have been nice if the scroll bars remained where you had them when you clicked the link or menu within a website. For example a slideshow which is compose of multiple pages but you want each time you click next and the page load to another content but then scroll top instead of where you were position (your initial click). However, sometimes you may want to load a new page on each click, either you want to make content more accessible to search engines for SEO purposes or want a dynamic feeling of your page.

With so many research i came accross of the script called Scroll Sneak developed by Peter Coles @ http://mrcoles.com/blog/scroll-sneak-maintain-position-between-page-loads/ . This script is  something that will generally go unnoticed when experienced, but it will also prevent the mild aggravation that arises from undesired functionality—a page jumping back to the top when you wished it didn’t.

## How it works

* click handlers are applied to the links that should maintain the scroll bar position between page loads
* when one of those links is clicked, the click handler grabs the scroll position from the window or document object and stores it as a string on the window.name object and then allows the new page to load
* when a page loads, the window.name object is checked to see if a scroll position has been stored in it, and if it has one, it scrolls to that position.

It also does some additional stuff, such as restoring an original window.name value if there was one before the click, and it does some automatic namespacing within the window.name object for some extra validation of stored scroll positions (probably overkill).

## Usage

### 1. In your ```<head> ``` tag put the following code in a <script></script>

```
var ScrollSneak = function(prefix, wait) {
    // clean up arguments (allows prefix to be optional - a bit of overkill)
    if (typeof(wait) == 'undefined' && prefix === true) prefix = null, wait = true;
    prefix = (typeof(prefix) == 'string' ? prefix : window.location.host).split('_').join('');
    var pre_name;

    // scroll function, if window.name matches, then scroll to that position and clean up window.name
    this.scroll = function() {
        if (window.name.search('^'+prefix+'_(\\d+)_(\\d+)_') == 0) {
            var name = window.name.split('_');
            window.scrollTo(name[1], name[2]);
            window.name = name.slice(3).join('_');
        }
    }
    // if not wait, scroll immediately
    if (!wait) this.scroll();

    this.sneak = function() {
	// prevent multiple clicks from getting stored on window.name
	if (typeof(pre_name) == 'undefined') pre_name = window.name;

	// get the scroll positions
        var top = 0, left = 0;
        if (typeof(window.pageYOffset) == 'number') { // netscape
            top = window.pageYOffset, left = window.pageXOffset;
        } else if (document.body && (document.body.scrollLeft || document.body.scrollTop)) { // dom
            top = document.body.scrollTop, left = document.body.scrollLeft;
        } else if (document.documentElement && (document.documentElement.scrollLeft || document.documentElement.scrollTop)) { // ie6
            top = document.documentElement.scrollTop, left = document.documentElement.scrollLeft;
        }
	// store the scroll
        if (top || left) window.name = prefix + '_' + left + '_' + top + '_' + pre_name;
        return true;
    }
}

```

### 2. Right before the closing </body> tag put the following code below in a <script></script>

```
// applies the sneak event handler to all "li" elements underneath the element with the id "tabs" which is swim-finder-item-list-sbb and also to the element with id "next" and "previous"
(function() {
    var sneaky = new ceScroll(location.hostname), //location.name can be any unique string
    	tabs = document.getElementById('swim-finder-item-list-sbb').getElementsByTagName('li'), 
    	len = tabs.length;

    for (var i = 0; i < len; i++) {
        tabs[i].onclick = sneaky.sneak;
    }
    
    document.getElementById('next').onclick = sneaky.sneak;
    document.getElementById('previous').onclick = sneaky.sneak;
})();

```

That's it... Enjoy!

## Reference/Source

http://mrcoles.com/blog/scroll-sneak-maintain-position-between-page-loads/

## Licensed

Licensed under the MIT licenses.






