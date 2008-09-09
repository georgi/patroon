Patroon - a Javascript Template Engine
======================================

Patroon is a template engine written in Javascript in about 100 lines
of code. It takes existing DOM nodes annotated with CSS classes and
expand a data object according to simple rules. Additionally you may
use traditional string interpolation inside attribute values and text
nodes.

### Example

Comments in this blog are stored as a list of JSON objects, I wrote about it [here][1]. So think about a data object like this:

    var data = { 
      comment: [{
        date: "2008-09-07 12:28:33", 
        name: "David Beckham",
        website: "beckham.com",
        text: "I watched the euro finals on tv..." 
      }, { 
        date: "2008-09-07 14:28:33", 
        name: "Tuncay",
        website: "",
        text: "Me too"
      }]
    };
    

This data will be expanded with help of following template:

        <div class="comments">  
          <div id="comments-template">
            <div class="comment">
              <div class="_ top">
                <a class="_" href="{website}">{name}</a> said
                <a class="_" title="{time}"></a>:
              </div>
              <div class="text"></div>
            </div>   
          </div>
        </div>

The javascript to actually execute this template looks like this:

    // The comments template will be removed from the DOM!
    var template = new Template('comments-template');
    
    // template will result in a new DOM node
    var result = template.expand(data);
    
    // insert the resulting node into the comments container
    var container = document.getElementsByClassName('comments')[0];
    container.appendChild(result);


Using jQuery the code gets a bit cleaner:

    // The comments template will be removed from the DOM!
    var template = new Template('comments-template');
    
    // Expand the template into the comments section
    $('.comments').expand(template, data);
    
    

### Basic Rules

There are 5 basic rules:

*   Strings and Numbers are inserted by innerHTML to the current node.

*   Arrays repeat the current node and process its elements recursively in same scope.

*   Objects trigger a class name lookup by property name. The value of each property is expanded recursively in new scope.

*   Code will be evaluated for text surounded with braces (Works also for attributes).

*   Processing child nodes will be triggered for nodes with a class attribute starting with `_`.

I admit the last point is a bit quirky, but processing all child nodes per default is too expensive and too unpredictable. Maybe I will find a better way, but I don’t mind inserting these extra `_` class names.

### Evaluation

So speaking of the example data, this would mean following algorithm:

*   Find the first node with a class name of comment.

*   Repeat the this node two times and recursively process the first and second comment.

*   Descend into the node with class `top`.

*   Descend into the first link node.

*   Evaluate the code found in the *href* attribute and in the text.

*   Descend into the second link node.

*   Evaluate the code found in the *title* attribute.

*   Find the first node with the class `text`.

*   Insert the the text of the comment into the found node.

Note that we are dealing with two scopes here: the global scope and the comment scope. The global scope just contains a name *comment* and the comment scope contains the names `date`, `name`, `website` and `text`.

### Download

Download the script at my [github repository][2].

 [1]: commenting-system-with-lightweight-json-store.html
 [2]: http://github.com/georgi/patroon/tree/master
