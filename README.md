# NOW That's What I Call Backbone
We have come a long way from our mixtape days. You're a little older, wiser and now know some new, slick technologies.

With this exercise, we are going to make a backbone application (from scratch!) that will display the track list of a mix CD. To make it even more slick, when we click on a song title, the artists name will appear below the title. WILD!

The goal of this exercise is to gain exposure to *some* of the Backbone vocabulary and concepts that we will exploring in depth in the next few weeks. **The goal of this exercise is not to fully understand every Backbone component!! We will do this in the following week of class.**

Note that if you get stuck at any point, look at the `solution` branch of this repository which has the full implementation. We don't want you to struggle a great deal with syntax and errors. Definitely copy and paste!

Let's jump in!


### Create the files
In the terminal:
```Bash
mkdir now-thats-what-i-call-backbone
cd now-thats-what-i-call-backbone
touch index.html
touch app.js
```

### Link Dependencies
A benefit of working with Backbone is it's flexibility. It allows us to continue working with familiar libraries, like jQuery and Underscore! In order to use them, we need to link them in our HTML.

Create your HTML shell. Then, using a CDN, link to jQuery, underscore and backbone at the bottom (right below the ``<body>`` tag).
After all our libraries have been linked, link to your js file, ``app.js``.

```html
<!--STEP ONE: setup HTML shell  -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>NOW That's What I Call Backbone</title>
  </head>
  <body>
  </body>

  <!-- STEP TWO: link all dependencies (underscore.js, jQuery.js and Backbone.js) -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/underscore.js/1.8.3/underscore.js" type="text/javascript"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.2.1/jquery.js" type="text/javascript"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/backbone.js/1.3.3/backbone.js" type="text/javascript"></script>

  <!-- STEP THREE: link the app.js file you created from the terminal -->
  <script src="app.js" type="text/javascript"></script>
</html>
```


### Initial Data
Our mix cd will be based on the following array of objects, where each object represents a song.

Copy this code to the top of your ``app.js`` file:
```JavaScript
var songData = [
    {
        "title": "Drop It Like It's Hot",
        "year": 2004,
        "artist": "Snoop Dog"
    },
    {
        "title": "Pieces of Me",
        "year": 2004,
        "artist": "Ashlee Simpson"
    },
    {
        "title": "Vertigo",
        "year": 2004,
        "artist": "U2"
    },
    {
        "title": "When the Sun Goes Down",
        "year": 2004,
        "artist": "Kenny Chesney"
    },
    {
        "title": "1, 2 Step",
        "year": 2004,
        "artist": "Ciara"
    },
    {
        "title": "Talk About Our Love",
        "year": 2004,
        "artist": "Brandy"
    },
    {
        "title": "It's My Life",
        "year": 2004,
        "artist": "No Doubt"
    },
    {
        "title": "Toxic",
        "year": 2004,
        "artist": "Britney Spears"
    },
    {
        "title": "Are You Going to Be My Girl",
        "year": 2004,
        "artist": "Jet"
    },
    {
        "title": "Redneck Woman",
        "year": 2004,
        "artist": "Gretchen Wilson"
    },
    {
        "title": "Lean Back",
        "year": 2004,
        "artist": "Terror Squad"
    }
  ];
```



### Models
Lets start with something we're familiar with, models! We are going to take the songData and turn each object into an instance of a Song model.

To create a model object, we create a "class-like" JavaScript variable which extends the Backbone Model object: ``Backbone.Model.extend({ });``.

In the code below we create a new model for our songs, then create a single song model instance by passing in an object with the expected properties.

Once the song is an instance, we can call the ``.get()`` function to return one of it's attributes, like we did with the console.log at the bottom of this code snippet.

```javascript
// app.js
var Song = Backbone.Model.extend({ });

var song1 = new Song({
  title: "Drop It Like It's Hot",
  year: 2004,
  artist: "Snoop Dog"
});

console.log(song1.get("title") +  " is by: " + song1.get("artist"));
```

We don't only have one song though.. so this is where collections come in!

### Collections
Collections allow us to handle many instances of a model in a more efficient way. We want many songs on our cd, instead of a single song, let's make a collection!

Much like the model, starting a collection requires us to extend the Backbone Collection object. All we have to do is set another capitalized, camel-cased variable to ``Backbone.Collection.extend({});``.  Inside, we set the collection up to point to the *model* this is a collection of, in this case, it is the Song object.

When we create a new collection, we can pass an array of objects (like songData) as an argument. Doing so will create all the new song models for us!

```javascript
// app.js
var MixCd = Backbone.Collection.extend({
  model: Song
});

// Fly new mix CD of songs for the summer
var summer04 = new MixCd(songData);

// See all the songs' titles displayed in console!
summer04.each(function(song) {    // Backbone has an each function
  console.log(song.get("title")); // We can access each property using a get function
});
```
All of our song data is now nicely organized, but we still don't see anything! This is partially where our views come into play.

## Views
### HTML Templates

Let's create our view with something familiar, HTML and underscore. We want to dynamically show the list of song elements from the array of data we created at the beginning.

To do this, we will use an empty `ul` tag to populate dynamically with `li` elements, containing the data from our collection of songs. We will create the `li` elements using an Underscore template. Our Underscore template is called `song-template` and will inject a song's title into our `ul`.

The following code should go between your index.html's ``<body>`` tags.

```html
<header>
  <h1> Ada's Totally Awesome Summer '04 Mix CD </h1>
</header>
<main>
  <ul class="song-list"> </ul>
</main>

<script id="song-template" type="text/template">
  <h3><%- title %></h3>
</script>
```


### Backbone View
Now back to JavaScript! The following instructions define how we are going to utilize a Backbone View. Note that the functionality described here is *a lot* more than what we are used to with a Rails view. Views in Backbone are a lot more like Controllers in Rails.

1. **Create the View**  
First, we are creating a new Backbone View for a song by extending the Backbone View object: `Backbone.View.extend`.

1. **Create the `initialize`**  
In the initialize function, we set the template by passing it as an argument. This variable will eventually be set to the #song-template when we create a new songView.

1. **Create the ability to render**  
We then define a `render()` function that we will call when we are ready to display our song in the DOM.

1. **Define our events and the corresponding event handlers**  
Another responsibility the view handles are click events! We first define the events, then define the callback functions.

```javascript
var SongView = Backbone.View.extend({
  initialize: function(params) {
    this.template = params.template;
  },
  render: function() {
    // console.log(this.model.attributes.artist);
    var compiledTemplate = this.template(this.model.toJSON());
    // console.log(compiledTemplate);
    this.$el.html(compiledTemplate);
    return this;
  },
  events: {
    'click h3': 'seeArtist'
  },
  seeArtist: function(e) {
    this.$el.append(this.model.attributes.artist);
  }
});
```

We are still not going to see anything in the browser window quite yet.  We still need to call the `render` function on each song! We will do this within a view that we create for our collection of songs.


### Collection View
This has a similar structure to the SongView, but one major difference that we are going to create new `SongView` instances in the `render` function. Then we have each of the instances append to our song-list `ul`.

You may have noticed that we never wrapped the `h3` element in our underscore template with an `li` element. Because we don't need to!

When we create the new songView instances, we're passing in an object with a few properties:
- The song model instance
- The underscore template
- The HTML element we want to use as the containing element for all of the HTML in the view. Another way to describe this is that all of the HTML we defined _inside_ of this view, will be put inside the `tagName` element, which is an `li` in this case. Note that this does not need to be an existing element in our DOM, it can be totally new and fresh!

```javascript
var MixCdView = Backbone.View.extend({
  initialize: function(params) {
    this.template = params.template;
  },
  render: function() {
    var that = this;
    this.model.each(function(song) {
      var songView = new SongView({
        model: song,
        template: that.template,
        tagName: 'li'
      });
      that.$('.song-list').append(songView.render().$el);
    });
    return this;
  }
});

```


### HOT DOM!
Now is the time where we can see our data come alive in the browser window!  

Now create a new instance of the collection view and call `.render()` on it. In the render function, an object with the following properties is being passed in:
- **Model**: The collection of songs we made earlier, ``summer04``
- **Template**: The Underscore template we set in the HTML and gave an id of 'song-template'
- **el**: This the element where we want to append all of our songs to. It will be the parent element to our songs view and exist already in our DOM (unlike the `li` in our `SongView`).


Once we create the collection view, we have to call the ``.render()`` function on the collection view in order to have our list of songs load to the DOM. Only then can we see our list of songs come alive in the browser window!

```javascript
$(document).ready(function() {

  var mixCdView = new MixCdView({
    model: summer04,
    template: _.template($('#song-template').html()),
    el: 'main'
  });

  mixCdView.render();
});
```
Click on a few of the song's titles and crack open dev tools and look around at all the element in the DOM.


### Now You've Done It!
To see all the code together, checkout the 'solution' branch of this repository.


### Conclusion
This walkthrough was a friendly introduction to three major components (models, collections and views) that go into organizing JavaScript code with Backbone and how they connect with each other.

Next week we will dive deeper into everything covered in this exercise, including splitting all of these components out into separate folders and files. If you feel like you gained exposure to the vocabulary and at a very high level see what these Backbone components _could_ be used for, you're **right on track**!!!

#### Lecture Resources
- [Introduction to Backbone](https://github.com/Ada-Developers-Academy/textbook-curriculum/blob/backbone-week1/11-Backbonejs/Introduction-to-Backbonejs.md)
- [Backbone Models](https://github.com/Ada-Developers-Academy/textbook-curriculum/blob/backbone-week1/11-Backbonejs/Backbone-Models.md)
- [Backbone Collections](https://github.com/Ada-Developers-Academy/textbook-curriculum/blob/backbone-week1/11-Backbonejs/Backbone-Collections.md)
- [Backbone Views](https://github.com/Ada-Developers-Academy/textbook-curriculum/blob/backbone-week1/11-Backbonejs/Backbone-Views.md)
-[Backbone Collection Views](https://github.com/Ada-Developers-Academy/textbook-curriculum/blob/backbone-week1/11-Backbonejs/Views-of-Collections.md)
