Patroon - a Javascript Template Engine
======================================

Patroon is a template engine written in Javascript in about 100 lines
of code. It takes existing DOM nodes annotated with class names and
expand a data object according to simple rules. Additionally you may
use traditional string interpolation inside attribute values and text
nodes.

### The Data

Comments in this blog are stored as a list of JSON objects, I wrote
about it [here][1]. So think about a data object like this:

    var data = { 
      comment: [{
        time: "2008-09-07 12:28:33", 
        name: "David Beckham",
        website: "beckham.com",
        text: "I watched the euro finals on tv..." 
      }, { 
        time: "2008-09-07 14:28:33", 
        name: "Tuncay",
        website: "",
        text: "Me too"
      }]
    };
    

### The Template

This data will be expanded with help of following template:

        <div class="comments">  
          <div id="comments-template">
            <div class="comment">
              <div class="top">
                {website.length > 0 ? linkTo(name, website) : name} said
                <a title="{time}"></a>:
              </div>
              <div class="text">
                {text}
              </div>
            </div>   
          </div>
        </div>


### Usage

The javascript to actually execute this template looks like this:

    // The comments template will be removed from the DOM!
    var template = new Template('comments-template');
    
    // Expand the template into the comments section
    $('.comments').expand(template, data);


If you don't want to use jQuery, please look at the end of this article.


### Output

The given example renders following output:

        <div class="comments">  
          <div id="comments-template">
            <div class="comment">
              <div class="top">
                <a href="http://backham.com">David Beckham</a> said
                <a title="2008-09-07 12:28:33">2 hours ago</a>
              </div>
              <div class="text">
                I watched the euro finals on tv...
              </div>
            </div>   
            <div class="comment">
              <div class="top">
                Tuncay said
                <a title="2008-09-07 14:28:33">1 minute ago</a>
              </div>
              <div class="text">
                Me too
              </div>
            </div>   
          </div>
        </div>
    
    

### Basic Rules

There are 3 basic rules regarding the evaluation:

* Each found class name of a node will be looked up in the current
  data object. If found, the node will be processed in the new scope.
  Example: the class name `comment` instructs to lookup the name
  `comment` in the data object, which contains the comment array.

* Arrays repeat the current node and process its elements recursively.

* Code will be evaluated for text surrounded with braces (works also
  for attributes). The evaluation takes place in the scope of the
  current data object, which is in the example a comment object. So
  the snippet `<a title="{time}">` will lookup the time in the comment
  object and insert into the title attribute.

### Helper

Code snippets inside the template will be executed within the scope of
a Helper object. If you want to extend it, just add your functions to
`Template.Helper`. At the moment it defines only one function:

    Template.Helper = {
     
        linkTo: function(text, url) {
            if (url.indexOf('http://') == -1 && url[0] != '/' && url[0] != '#') {
                url = 'http://' + url;
            }
            return '<a href="' + url +'">' + text + '</a>';
        }
     
    };

### Download

Download the script at my [github repository][2].

 [1]: commenting-system-with-lightweight-json-store.html
 [2]: http://github.com/georgi/patroon/tree/master


### Without jQuery

Without jQuery template expansion is a bit verbose:

    // The comments template will be removed from the DOM!
    var template = new Template('comments-template');
    
    // template will result in a new DOM node
    var result = template.expand(data);
    
    // insert the resulting node into the comments container
    var container = document.getElementsByClassName('comments')[0];
    container.appendChild(result);

