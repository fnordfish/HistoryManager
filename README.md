HistoryManager
================
This library consists of 2 Classes:

  * HashListener : Supplies a very simple interface to mimic a cross-browser back/farward buttons functionality through the browser hash. It uses only 1 method to do this - updateHash, and only 1 Event - 'hash-changed'.
  * HistoryManager : This class is built on-top of the HashListener to supplly an Observer interface to the HashListener.

I've patched quite a lot of code together to implement the HashListener (and probably will find myself patching some more later on).
The Main code sources i used are:

  * Dave De Vos supplied me with a very good Moo implemenetation that he worked out from several other sources (such as YUI and LittleHistoryManager)
  * Digitarald's HistoryManager, which was originaly made for Moo 1.1. His IE method is by now the best I've seen used and is the one used for this class.

The class started as a prooof-of-concept for a Domain Observer. Thus, this class still needs a lot of testing to be made. 
  
Tested With: FF3.5, Safari 4.0.4, Chrome 3, Opera 10.10 and IE6, IE7 emulation (throught the IE8 developer interface) and IE8. 

![Screenshot](http://github.com/arieh/HistoryManager/raw/master/screenshot.png)

How To Use
-------------
For both classes, you must supply a blank.html (comes with the package), and point the classes to it (for IE<8 support). by default the classes assume it's location at the root dir.

### Hash Listener

This class supplies a very simple interface for modifying the browser hash in a way that supports a back/farward behavior to all browsers (theoretically).
How To Use:

	#JS
	var HM = new HashListener();
	
	//add an event listener to the manager
	HM.addEvent('hash-changed',function(new_hash){
		console.log(new_hash);
	});
	
	HM.start(); //Will start listening to hash changes
	
	HM.updateHash('a string'); //will log 'a string'
	HM.updateHash('another string'); //will log 'another string'
	
note that updateHash changes the hash completely.

### History Manager

This class extends the HashListener to supply a richer interface that allows multiple states to be kept together.
It supplies a Domain Observer. This means that you can register your classes through it, and let it transact data between different classes and layers of the site. 
It's usage can be a bit confusing but it actually tries to use JavaScript's event driven syntax:
	
	#JS
	var HM = new HistoryManager();
	
	/*
	 * Adding events to the observer. 
	 * Should be done before observer is started, so that they will 
	 * also be used when site is opened from history/bookmark.
	 */
	HM.addEvent('someValue-added',function(new_value){
		console.log('someValue was added:'+new_value);
	});
	HM.addEvent('someValue-changed',function(new_value){
		console.log('someValue was changed:'+new_value);
	});
	HM.addEvent('someValue-removed',function(last_value){
		console.log('someValue was removed:'+new_value);
	});
	
	HM.start();
	
	HM.set('someValue',1); //will log 'someValue was added:1'
	HM.set('someValue','aaa'); //will log 'someValue was changed:aaa'
	HM.remove('someValue'); //will log 'someValue was removed:aaa'
	HM.set('someValue','bbb'); //will log 'someValue was added:bbb'
	

Note that this can be done with multiple value names, and can use any JSON-encodable data format as values (such as strings, arrays and objects).
	
Options
---------
Both classes use the same options:

  * blank_page : an alternative source for an iframe file. *note that the file must be valid for IE<8 support*
  * start : whether to start service on creation (default:false). this is not recomended, since you want the events to be registered before starting the class up.

Events
-------
### Hash Listener

  * 'hashChanged' : will fire whenever the hash was changed (whether by the back-button or the class's methods). will send the new hash as a paramater to the function

### History Manager
Along side the `hashChanged` event, the class supports 3 dynamic Events, which point to the 3 states a value might hold ('*' notes the value's name):

  * ' * -added' : Will fire when a new value is added. Will send the new value to the function.
  * ' * -changed' : Will fire when a value is modified. Will send the new value to the function.
  * ' * -removed' : Will fire when a value is removed. Will send the last value to the function.
