# Tunr 1.0!

We're going to be building Tunr, the worlds #1 music catalog / player (those
Spotify haters can't keep up with us).

## Part 1 - Database / Schema

In this exercise, your goal is to build out the schema for tunr, load it with
seed data, and run some sample queries to explore the data.

### Create the database

Create a postgres database called `tunr_db`. Make sure to use that **exact**
name, or you'll have trouble later on!

### Create a schema

Inside the db folder, create a `schema.sql` file, and then load it into your
`tunr_db` database.

Here's what our data model looks like:

*Artists*

| column name  | type |
|--------------|------|
| id   | primary key (int) |
| name | text |
| photo_url | text |
| nationality | text |

*Songs*

| column name  | type |
|--------------|------|
|id | primary key (int) |
|title | text |
|album | text |
|preview_url | text |
|artist_id | foreign key (int) |

### Load the Seed Data

Use psql to load the seed data located in `db/seed.sql`

### Play with the data

Using `psql`, play with the database, come up with some interesting sample
queries, and save them in a file in the db folder called `sample_queries.sql`

## Part 2.1 - Create the Artist Model using Active Record

1. Create an app.rb file for this application
2. Create a Gemfile using `bundle init`
3. Create a folder for your models (it should be called `models`)
4. Create a file that will contain your AR class definition for 'artists'
5. Add your dependencies into the Gemfile and then run `bundle install`


## Part 2.2 - Define artists & setup your app.rb to connect to the database

1. Define your artist model in `models/artist.rb`
2. Require your dependencies in `app.rb`
3. Establish connection to database using AR
4. Load pry at the end of `app.rb`

## Part 2.3 - Use your Artist Model

In the console:

1. Find all artists
2. Find just one artist by id
3. Find Taylor Swift (or your other fav artist) by **name**.
4. Find all artists from the USA
5. Find all artists NOT from the USA
6. Create a new artist for your favorite artist
7. Change at least 2 of their attributes
8. Destroy the artist you just created
  - (NOTE: If you destroy other artists at this point, you'll need to reseed your data for consistency.)

## Part 2.4 - Create your Song Model / Setup Associations

1. Create a file that will contain your AR class definition for Songs
2. Make sure to link that file in your main application file
3. Add corresponding associations to your models

## Part 2.5 - Use your Model Associations

In the console...  

1. Find the artist with the name Enya
2. Use AR methods to find all of Enya's songs, store them in a variable
3. Get the first song out of those results and get that song's title
4. Find the song with the title 'Unstoppable' and store in a variable
5. Get that song's artist, store that in a variable
6. Reassign the song's artist to be a different one (your choice)
7. Save that song
8. Create a new song, and associate it with a different artist of your choice
9. Delete that song
10. Find all of Enya's songs again, store in a variable
  - Using `each`, iterate over those songs and for each song, print "I like" + the song name

## Part 3 - Build a RESTful Interface using Sinatra

First you wrote the schema for a database to store records of artists and their songs for a music catalog app. You then seeded the database with sample data and we were able to interact with that data (performing CRUD operations) using SQL queries in Postgresql. Next, you connected ActiveRecord to the database which enhanced your ability to interact with the data in a Ruby environment. Still, our only way to interact with the data is in a console.

Today, you are going to use Sinatra to listen for HTTP requests and serve HTML (rendered from ERB templates) in response.

### Part 3.1: Root Route, Home View, and Layout View

1. Run your application: `ruby app.rb` and go to localhost:4567 in your browser... Sinatra doesn't know this ditty.

  - First just define the route in the General Routes section of app.rb as described by Sinatra:
    ```ruby
    get '/' do
      "Hello World"
    end
    ```
  Now head back to localhost:4567 and check it out!

  - So maybe not super exciting but moving in the right direction

  - Replace `"Hello World"` with HTML rendered from an ERB template, home. Our General Routes section of app.rb now reads:
    ```ruby
    get '/' do
      erb :home
    end
    ```

- [erb](http://www.sinatrarb.com/intro.html#Views%20/%20Templates) is a method provided by Sinatra which uses Ruby's [ERB (Embedded RuBy)](https://en.wikipedia.org/wiki/ERuby) templating system to take a template string (text with ruby written in it) and produce an output string after evaluating the embedded ruby.

- If we tried to go to localhost:4567 now, we'd get an error saying __No such file or directory @ rb_sysopen - /path/to/tunr_sinatra/views/home.erb__

    - When we give the `erb` method an argument of `:home`, the method looks in the __views/__ directory of the project for a file named home.erb and takes the contents of that file as its template string.

    - Make that __views/__ directory and add a file home.erb.

    - In home.erb, first add some HTML (no need to worry about embedding ruby just yet) and check it out at localhost:4567

- The erb method will also check for a layout.erb file where you define the part of the template shared by all the pages in our app (e.g. head/body elements, header/footer, navigation bar, etc). In layout.erb need to include a line of embedded ruby with the keyword `<%= yield %>`. This tells the method to render the named template (`:home`) at this point in the layout.erb template. In the below steps, regularly check out localhost:4567 and consider the effects of your code changes

    - Make a layout.erb file in views/

    - As always, start simple, maybe just a heading. Check it out in the browser

    - Then expand adding head and body elements, layout.erb should produce well formed HTML and since there is no embedded ruby in it yet, it should be well formed HTML

    - Create a __public/__ directory in the root of your project. By default, Sinatra looks for static assets in a directory named __public/__ in the root directory of the project. In the __public/__ create a stylesheet.

    - Back in layout.erb add a link to your stylesheet in the head. In the body add a nav element containing links to '/artists' and '/songs'. Finally below the nav element, add an embedded ruby expression calling `yield`.

    - Take a moment using your browser's dev tools to look at how the HTML rendered in from home.erb nests into the HTML rendered from layout.erb where we included `<%= yield %>`.

    - Spend a few minutes making this page look nice/pleasant -- as you keep working on the app, you're going to see it a whole bunch

### Part 3.2: Artist Controller and Views

1. Make a __controllers/__ directory in __tunr_sinatra/__ and create a file __artists.rb__ which will hold the routes for your artists

  - We have talked about CRUD operations as covering most of what we want to do with data: Create, Read, Update, and Destroy.

  - You want to allow your user means to perform these four operations on the artists in your database. In this section you will define seven routes for artists which allow your user to perform these actions:

    1. Index (index all artists) - GET "/artists"

    2. Show (show particular artist) - GET "/artist/:id"

    3. New (render form to create new artists) - GET "/artists/new"

    4. Edit (render form to edit existing artist) - GET "/artists/:id/edit"

    5. Create (submit form to create new artist) - POST "/artists"

    6. Update (submit form to update existing artist) - PUT "/artists/:id"

    7. Destroy (delete an existing artist) - DELETE "/artists/:id"

- Make your __Index__ route for artists

  - In the browser, try to follow the link to "/artists" in your nav bar. You see Sinatra doesn't know this ditty. Not shocking as you have not yet defined it.

  - While it is possible to put the "/artists" route in app.rb, it helps keeps thing organized to keep a separate file where we define all of our artist routes and then use `require_relative` to import that file. These kinds of files which handle interaction between the our models and our views are our controllers so make a __controllers/__ directory and create a file artists.rb. Put in the route Sinatra suggests
    ```ruby
    get "/artists" do
      "Hello World"
    end
    ```

  - Be sure to add the following in your app.rb
    ```ruby
    require_relative 'controllers/artists.rb'
    ```

  - Check out the browser again and see the greeting "hello world".

- Make your __Index__ view for artists.

  - Now we want to serve a better response.

  -  `"Hello World"` with `erb :"artists/index"`. Now Sinatra is looking for `/path/to/tunr_sinatra/views/artists/index.erb` (the reason we organize our views this way will become apparent as you make more). Make the artists/ directory inside of views/, and create the file index.erb in the views/artists/ directory

  - Add a heading to the artists index page.

  - As home.erb did, the artist/index.erb template will render nested within our layout.erb template.

- Now in the controller you want to get data from the database (by means of ActiveRecord and our models) and use it to render the views (using erb).

  - 
