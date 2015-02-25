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

and you must draw each `<input>` inside using helper methods like so:

    <%= f.[INPUT TYPE] [COLUMN NAME] %>

e.g.,

    <%= f.text_field :source %>

You can also quickly create a matching label for each `<input>`:

    <%= f.label :source %>

Finally, there's a helper for the submit button, too:

    <%= f.submit %>

In Chrome, **view source** and study the code that these helper methods are writing for you. How is it different from what `form_tag` writes?

## Part 7: Create action

When you click the submit button on the new form, `form_for` is smart enough to guess that the action should be to `POST` to `/photos`, since that is the RESTful convention.

Implement the `create` action to use the form data to add a row to the photos table.

Two new things:

 1. `form_for` wraps up all the photo-related form inputs into a subhash, and nests it under the `params[:photo]` key. Check out your server log to see this in action.
 1. You need to whitelist the parameters that we accept from the internet before mass-assigning them, like so:

        params.require(:photo).permit(:caption, :source)

## Part 8: Edit form

I want to be able to visit [http://localhost:3000/photos/1/edit](http://localhost:3000/photos/1/edit), etc, and see a form to edit an existing photo.

Use the `form_for` helper method rather than the `form_tag` helper that we are used to.

## Part 9: Update action

When you click the submit button on the edit form, `form_for` is smart enough to guess that the action should be to `PATCH` to `/photos/1`, etc, since that is the RESTful convention.

Implement the `update` action to use the form data to update the row in the photos table.

## Part 10: DRY up the forms into a partial

Since the new and edit forms are now identical (because we are using `form_for`, which guesses the correct action, HTTP verb, and even button text), we can DRY them up into one place.

If you haven't already, create a partial view template `app/views/photos/_form.html.erb`, and paste in the `form_for` code.

In the new and edit templates, use the partial:

    <%= render 'form' %>

## Part 11: Add links

So far, we have no links in this application, which makes navigating it very inconvenient.

For each photo, add a link to show, edit, and delete on both the show and index pages.

On the index page, add a link to the new form at the top.

Notes:

 1. Use `link_to` helper methods, not raw `<a>` tags.
 1. The delete links will need to use the HTTP method `[DELETE]` in order to trigger the `destroy` action RESTfully.
 1. You can use some extreme shortcuts with `link_to`. Study the following evolution of conciseness; each line produces roughly the same source code.

        <a href="/photos/1">Show</a>
        <%= link_to "Show", /photos/1" %>
        <%= link_to "Show", photo_path(zebra.id) %>
        <%= link_to "Show", photo_path(zebra) %>
        <%= link_to "Show", zebra %>

        # Assuming that zebra is an ActiveRecord object, and
        #   that routes are RESTful and named conventionally.

    The last shortcut, where we supply an ActiveRecord object as the second argument to `link_to` where it usually expects a URL, only works if you are trying to create a link to the show page of a row. For any other URL, you have to used the second-to-last shortcut, e.g.,

        <%= link_to "Edit", edit_photo_path(photo) %>

## Part 12: Destroy

Make the delete links work.

## Part 13: Error handling

Add a validation to the Photo class:

    validates :source, :presence => true

Now, if you try to create a photo with no source, it shouldn't save. However, we aren't telling the user why the operation is failing. Let's improve the UX.

In the `create` action, do one of two things:

 - If the photo saves properly, redirect to the index page with the notice "Photo saved successfully". Add the `<%= notice %>` view helper in the application layout file if you haven't already so that this message shows up, if present, at the top of every page.
 - If the photo doesn't save properly, render the new form again.
 - At the top of the form, tell the user what went wrong. Remember that you can access a list of error messages, after a failed `.save`, with `.errors.full_messages`.

Do the same thing for the edit/update flow.

