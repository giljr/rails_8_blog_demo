## [Building a Rails 8 Blog App with Turbo Frames and Authentication](https://medium.com/jungletronics/building-a-rails-8-app-from-scratch-a090042fb91a)

Prerequisites:

    OS: Ubuntu 24.04 LTS or similar
    Ruby: Installed (v3.2+ recommended)
    Rails: Installed (v8.0.1)
    Database: SQLite (default for simplicity)

Setup Instructions:

    Create a New Rails App

rails new blog
    
    cd blog

Generate Post Scaffold

    rails g scaffold post title:string body:text
    rails db:migrate

Add Rich Text (WYSIWYG) Support

    rails action_text:install
    rails db:migrate

Update app/models/post.rb:

    has_rich_text :body

Modify form partial app/views/posts/_form.html.erb:

    <%= form.rich_text_area :body %>

Add Styles

Update app/views/layouts/application.html.erb:

    <%= stylesheet_link_tag "https://cdn.simplecss.org/simple.css" %>

Install Libvips for Image Uploads

    sudo apt-get update
    sudo apt-get install -y libvips

Enable Local Time Display
Install and configure local-time:

    bin/importmap pin local-time

Update app/javascript/application.js:

    import LocalTime from "local-time"
    LocalTime.start()

Generate Comments Resource

    rails g resource comment post:references content:text
    rails db:migrate

Update app/models/post.rb:

    has_many :comments

Update app/models/comment.rb:

    broadcasts_to :post

Add Comment Partials
Create the following files:

app/views/comments/_comment.html.erb:


    <div id="<%= dom_id(comment) %>">
        <%= comment.content %> - 
        <%= time_tag comment.updated_at, "data-local": "time-ago" %>
    </div>

app/views/comments/_comments.html.erb:

    <h2>Comments</h2>
    <div id="comments">
        <%= render post.comments %>
    </div>
    <%= render "comments/new", post: post %>

app/views/comments/_new.html.erb:

    <%= form_with model: [post, Comment.new] do |form| %>
        Your comment:<br>
        <%= form.text_area :content, size: "20x5" %> <br>
        <%= form.submit %>
    <% end %>

Display Comments on Post Show Page

Update app/views/posts/show.html.erb:

    <%= render "comments/comments", post: @post %>

Add Turbo Streams
Enable WebSocket subscription:

    <%= turbo_stream_from @post %>

Set Up Authentication
Generate authentication:

    rails g authentication
    rails db:migrate

Seed a user in db/seeds.rb:

    User.create!(email_address: "jay@gmail.com", password: "123")

Run seed script:

    rails db:seed

Add Navigation and Root Route
Update config/routes.rb:

    root "posts#index"
    resources :posts do
    resources :comments
    end

Add sign-out button in app/views/layouts/application.html.erb:

    <%= button_to 'Sign out', session_path, method: :delete if authenticated? %>

Run the Application
Start the server:

    rails s

Access the app at:
http://127.0.0.1:3000

Push to GitHub

    git init
    git add .
    git commit -m "Adding a Blog App with Turbo Frames and Authentication"
    git branch -M main
    git remote add origin <your-github-repo-url>
    git push -u origin main

Features:

    Rich text editing for posts
    Local time display with local-time
    Turbo Streams for real-time updates
    Authentication (custom solution)
    Commenting system with partials and Turbo Frames
    Simple styling with a CDN stylesheet

Enjoy your new Rails 8 blog app! 🎉

