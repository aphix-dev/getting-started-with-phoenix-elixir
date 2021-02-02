## Getting Started With the Phoenix Framework for Elixir


## Setting Up
To create a new Phoenix project (assuming that you already have Phoenix installed), you need to use the command
```
mix phx.new <project_name>
```
This will initialize a new Phoenix project and create a new folder containing that project within your current directory.

To generate the database for your project, use the command 
```
mix ecto.create
```
in the project directory. This will create your database. If you have an error authenticating, go to the `config/dev.exs` directory and make sure that your Postgres settings are valid.

After creating your database, use the command
```
mix ecto.migrate
```
to finish up the database setup.



## Creating a JSON "Model"
To create a new JSON Model, you will need to use the command
```
mix phx.gen.json <Category-Name> <Model-Name> <Model-Plural-Name> <values>
```
Let's create a new `Book` model.
```
mix phx.gen.json StoreAPI Book books title:string author:string rating:integer
```
Once you have run this command, a new elixir file (`/<category-name>/<model-name>.ex`), or `lib/books_api/store_api/book.ex` will be created. Now, run the command
```
mix ecto.migrate
```
to migrate your new model to your project's database.

In the new `book.ex` file, you have the new model's schema and changeset function. The changeset function is used to validate different fields and require different fields. For each schema key, you can define a default value. Let's use our books model as an example
```elixir
schema "books" do
 field :title, :string, default: "default_title"
 field :author, :string
 field :rating, :string, default: 5 
end 
```

Along with this new file created for the model's schema, there will also be a new view file that describes how to render the model (/<Project-Name>_web/<Model-Name>_view). We will leave this alone for now.

Under the /<Project-Name>_web/controllers/ directory, you will notice a new file named <model-name>_controller.ex. This handles the REST methods that we might use in our API.
```
index  -> get all the API elements
create -> create a new API element
show   -> find a certain API element
update -> update a certain API element
delete -> delete a certain API element
```
 
 
 
## Routing
Now, check out the file /lib/<project-name>_web/router.ex file. Uncomment the section that starts with `scope "/api"`. Here, you can handle routes to the `/api` route. Let's define a get router to get all of our books.

Under the part that says `pipe_through :api`, add this line
```
get "/books", BookController, :index
```
What this does is makes is so that whenenver we send a GET reqest to `localhost:4000/api/books`, we will call the index function of the BookController, which effectively returns all the items in our book database.



## Running The Server
Now, let's run our server using the console command
```
mix phx.server
```
If all is successful, our server will be listening on localhost:4000!

When you visit localhost:4000, you will be greeted with a Phoenix Framework home page, but not our API. Never fear - we just have to visit the correct route on our server. Let's go to localhost:4000/api/books, and you will be greeted by an empty piece of JSON! That means everything is set up correctly!

Now, all we have to do is add some stuff to our database so that we get some elements back from our API.



## Adding Test Elements To Our Database (seeding)
To add "test" elements to our API, let's add the following lines to the `/priv/repo/seeds.exs` file:
```elixir
# Replace <project-name> with your project name
# Replace <category-name> with the category you put your model under
alias <project-name>.Repo
alias <project-name>.<category-name>.Book

Repo.insert! %Book{
  title: "sample book",
  author: "sample author",
  rating: 3
}
```
This will create a new Book element in our StoreAPI database so we can see things change. Now, let's put this seeder into play by using the console command
```
mix run priv/repo/seeds/exs
```
Now, let's revisit our `/api/books` route on our server. If all is correct, you will now see our test element in our JSON object!
