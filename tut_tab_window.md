---
layout: default-withsidebar
title: Working with tabs and windows 
author: shwetankdixit
copyright: opera-ccby
---

##Introduction
The Opera Extension APIs provide a lot of power when it comes to manipulating windows and tabs inside the browser. In this article, you’ll take a deeper look at how to manipulate windows and tabs in extensions.

## Tabs
The ability to manipulate tabs is one of the most basic but powerful and useful features of extensions. The [Tabs and Windows API guide](tabs.html) provides a detailed overview of the methods and events associated with it. 

To work with tabs in an extension you need to first specify the relevant permissions in the *‘permissions’* field of the extension manifest, like so:

<pre class="prettyprint">{
  ...
  "permissions": ["tabs"],
  ...
}</pre>


### Creating a new tab
Creating a new tab is done using the `create()` method. Inside this method you can specify the properties of the tab you want to create (for example the URL, whether it should be an active tab, whether it should be a pinned tab or not, etc.) 

For example, to specify extension functionality that opens up a new pinned tab containing [opera.com](http://www.opera.com) when a button is clicked, you need to add this to the background.js file:

<pre class="prettyprint">chrome.browserAction.onClicked.addListener(function() {
  chrome.tabs.create({'url': ‘http://www.opera.com’});
});</pre>


You can [download the sample extension](samples/WinTabs-CreateATab.nex) and take a better look at how this works.

### Accessing the current tab
You can access the currently active tab and its properties via the `query()` method, for example:

<pre class="prettyprint">chrome.tabs.query({currentWindow: true, active: true}, function(tab)</pre>

Lets extend this example a bit. To specify that on the click of a button the browser will take the current URL and open a new tab where it will run the URL through the WAVE accessibility evaluation tool, you could do something like this: 


<pre class="prettyprint">chrome.browserAction.onClicked.addListener(function() {
  chrome.tabs.query({currentWindow: true, active: true}, function(tab) {
    chrome.tabs.create( { "url": "http://wave.webaim.org/report?url=" +tab[0].url } );
  });
});</pre>

You can [download the WAVE extension](samples/WinTabs-Wave.nex) and play with this code further.

### Modifying existing tabs
You can modify existing tabs using the `update()` method, performing tasks such as changing the URL loaded, making tabs active, highlighting, or pinning them. The `query()` method explained above is used to select the tab(s) you want to affect.

For example, imagine you wanted to create an extension that allows us to redirect all tabs on [Dev.Opera](http://dev.opera.com) (not just the front page, but various articles too) to the [Opera homepage](http://www.opera.com).

In the background.js, you would write something like:

<pre class="prettyprint">var pattern = "http://dev.opera.com/*"; // This pattern will match all tabs that point to URLs on dev.opera.com including subdomains if any

chrome.browserAction.onClicked.addListener(function() {
  chrome.tabs.query({'currentWindow': true, 'url': pattern}, function(tab) { // This will match all tabs to the pattern we specified
    for (var i=0; i&lt;tab.length; i++){ // Go through all tabs that match the URL pattern
      chrome.tabs.update(tab[i].id, {'url': 'http://www.opera.com'}); // Update those tabs to point to the new URL, which is the opera homepage
    }        
  });
});</pre>

 You can [download the update example extension](samples/WinTabs-UpdateTab.nex) to see the above code in action. 

### Closing, reloading and duplicating tabs
To close, reload and duplicate tabs, you need to use the `remove()`, `reload()` and `duplicate()` methods respectively. The important thing to note about these methods is that you do not necessarily need to mention the ‘tabs’ permission in the extension manifest in order to use them in the extension.

All three of these methods work in the same way. Lets look at some example extension code that reloads a tab. The first thing to do would be to get hold of the current tab, after which you would call the `reload()` method. So, in the background script you would write something like so:

<pre class="prettyprint">chrome.tabs.query({currentWindow: true, active: true}, function(tab){ // Get the current tab
  chrome.tabs.remove(tab[0].id); // Remove the tab
});</pre>

You can use the `remove()` and `duplicate()` methods in exactly the same way. Take a look at our [sample close, reload and duplicate extension example](samples/WinTabs-CloseReloadDuplicate.nex) where we make use of all three methods to close, reload and duplicate the current tab.

There are more functions available in Opera extensions: to find out about them, consult the [API guide for Tabs and Windows](tut_tab_window.html).

## Windows

Windows are easy to create using the Tabs and Windows APIs. The first thing to do when working with windows is to specify the necessary permissions in the extension manifest:

<pre class="prettyprint">{
  ...
  "permissions": ["tabs"],
  ...
}</pre>

**Note:** Yes, we specified \[‘tabs’] in the above manifest code: this is *deliberate*. Windows and tabs are so closely related that it is a good idea to mention ‘tabs’ in the extension manifest whenever you are working with windows.

The most common use case for the Windows API is to create a new window. Usually, when you create a new window, you'll want to open a bunch of tabs and URLs inside it. Lets create an extension that does exactly that.

The most common and hence most important function to note, when it comes to windows, is the `create()` function. It simply allows us to create a new window, specifying exactly what type of window we want. In this case, the code will create a new private window, with a few links already opened in it.

You can achieve this by writing the following in the background.js:

<pre class="prettyprint">var URL_list = ['http://www.opera.com', 'http://www.wikipedia.org', 'http://www.google.com'];//The list of URLs to load in the new window

chrome.browserAction.onClicked.addListener(function() {
  chrome.windows.create({'url': URL_list,'incognito': true});
});</pre>

[Download the sample window extension](samples/WinTabs-PrivateWindow.nex) to see the above functionality in action and play with the code yourself. 