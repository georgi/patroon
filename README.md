Patroon - a Javascript Template Engine
======================================

Patroon is a template engine written in Javascript in about 100 lines
of code. It takes existing DOM nodes annotated with context attributes
and expand a data object according to simple rules. Additionally you
may use traditional string interpolation inside attribute values and
text nodes.

### The Data

Comments in this blog are stored as a list of JSON objects, I wrote
about it [here][1]. So think about a data object like this:

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
    

### The Template

This data will be expanded with help of following template:

        <div class="comments">  
          <div id="comments-template">
            <div contect="comment" class="comment">
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

* The special node attribute `context` declares a scope, which will be
  looked up in the current data object.

* Arrays repeat the current node and process its elements recursively.

* Code will be evaluated for text surounded with braces (works also
  for attributes).


### Example Evaluation

So speaking of the example data, this would mean following algorithm:

* Find the node with `comment` context.

* Repeat this node two times and process the first and second comment.

* Evaluate the code found in text nodes and attributes within the
  scope of each comment.

Note that we are dealing with two scopes here: the global scope and
the comment scope. The global scope just contains a name *comment* and
the comment scope contains the names `date`, `name`, `website` and
`text`.

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

