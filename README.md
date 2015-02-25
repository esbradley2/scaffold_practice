# Scaffold Practice

In this project, we'll again practice building a RESTful, database-backed CRUD resource; but this time using advanced helper methods and techniques to create a DRY, best-practices-adhering codebase.

Ultimately, we want to write by hand and understand the code that Rails' built-in scaffold generator will write for us from here on out.

## Part 1: Create the photos table

Use the model generator to create a model file and migration file for a photos table:

    rails g model photo caption:string source:string

Don't forget to `rake db:migrate`.

## Part 2: Seed using mass-assignment

Scaffolds use the `form_for` helper method, which leverages *mass assignment* to write very concise controller actions.

Let's practice using mass assignment in the seeds file. I have included an array of info; use the array to create seven rows in the photos table.

Read the instructional comments at the very top of the file for hints on how to do it very concisely.

## Part 3: RESTful routes

We know we will need the Golden Seven, RESTful routes to support one full CRUD resource.

Let's use the new `resources` helper method to draw a perfect set in `routes.rb`:

    resources :photos

To actually see the routes that this helper draws for us, rely on the `rake routes` Terminal command.

## Part 4: Index page

I want to be able to visit [http://localhost:3000/photos](http://localhost:3000/photos) and see a list of the photos.

Complete this RCAV. There's nothing new in this one, except that the route already exists from the `resources` helper above.

## Part 5: Show page

I want to be able to visit [http://localhost:3000/photos/1](http://localhost:3000/photos/1), etc, and see the details of one image.

Complete this RCAV. There's nothing new in this one.

## Part 6: New form

I want to be able to visit [http://localhost:3000/photos/new](http://localhost:3000/photos/new), and see a form to create a new photo.

Use the `form_for` helper method rather than the `form_tag` helper that we are used to.

Remember, `form_for` looks like this:

    <%= form_for [ACTIVERECORD OBJECT] do |f| %>

    <% end %>

and you must draw each `<input>` using helper methods like so:

    <%= f.[INPUT TYPE] [COLUMN NAME] %>

e.g.,

    <%= f.text_field :source %>

Finally, there's a helper for the submit button, too:

    <%= f.submit %>

## Part 7: Create action

When you click the submit button on the new form, `form_for` is smart enough to guess that the action should be to `POST` to `photos_url`, since that is the RESTful convention.

Implement the `create` action to use the form data to add a row to the photos table.

Two new things:

 1. `form_for` wraps up all the photo-related form inputs into a subhash, and nests it under the `params[:photo]` key.
 1. You need to whitelist the parameters that we accept from the internet like so:

        params.require(:photo).permit(:caption, :source)

