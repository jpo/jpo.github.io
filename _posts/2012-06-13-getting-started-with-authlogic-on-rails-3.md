---
layout: post
title: Getting Started With Authlogic on Rails 3
---

[Authlogic][1] is “a clean, simple, and unobtrusive ruby authentication solution”. I’ve used it successfully in several Rails projects. It’s easy to setup, it’s easy to test, it’s flexible, and it has decent documentation. There’s even an [example application][2].

In this post, I’m going to walkthrough how to quickly get up and running with Authlogic on Rails 3. This guide is setup so that at the end of each step you will have working software. When you’re finished, you will have a basic application that can authenticate users. Let’s get started!

## Prerequisites

To follow along in this example, you’ll want to make sure that you have the following installed:

{% highlight text %}
Ruby 1.9.3
Rails 3.2.3
Bundler 1.1.0
{% endhighlight %}

## Step 1: A New Rails Project

The first thing we’re going to do is generate a new Rails application. Open your terminal and run the command below in the directory of your choice.

{% highlight bash %}
rails new authlogic_example
{% endhighlight %}

Next, let’s configure our new project to use the Authlogic gem. In your text editor, open the ```Gemfile``` add the line below.

{% highlight ruby %}
gem 'authlogic'
{% endhighlight %}

Finally, let’s install the gem using Bundler. In your terminal, run the command below.

{% highlight bash %}
bundle install
{% endhighlight %}

Let’s test our changes. In your terminal run the rails server command. Then, open you browser and navigate to ```http://localhost:3000```. If the application is configured properly you should see the Rails welcome page.

## Step 2: The Models

Our application will use two models: ```User``` and ```UserSession```. The ```User``` model will be responsible for persisting users in the database and the ```UserSession``` model will be responsible for authenticating users and managing their sessions.

To generate these models, run the commands below in your terminal.

{% highlight bash %}
rails generate model User
rails generate authlogic:session UserSession
{% endhighlight %}

Several files should have been created. For now we’re only interested in the ```CreateUsers``` migration that was created in the ```db/migrate``` folder. We’re going to add some columns that Authlogic will recognize and use to authenticate our users.

In your text editor, modify the ```CreateUsers``` migration to match the example below.

{% highlight ruby %}
# db/migrate/*_create_users.rb
class CreateUsers < ActiveRecord::Migration
  def change
    create_table :users do |t|
      t.string :email, null: false
      t.string :crypted_password, null: false
      t.string :password_salt, null: false
      t.string :persistence_token, null: false
      t.timestamps
    end
end
{% endhighlight %}

Now let’s configure the User model. We’ll add the ```acts_as_authlogic``` macro to let Authlogic know that this is the model we’ll be authenticating with. We’ll also use ```attr_accessible``` to make the ```email```, ```password```, and ```password_confirmation``` attributes accessible to our controllers.

In your text editor, modify the ```User``` model to match the example below.

{% highlight ruby %}
# app/models/user.rb
class User < ActiveRecord::Base
  acts_as_authentic
  attr_accessible :email, :password, :password_confirmation
end
{% endhighlight %}

Next, we’re going to modify our database seed file and create a test user that we’ll use to authenticate. In your text editor, modify the seed file to match the example below.

{% highlight ruby %}
# db/seeds.rb
User.create(email: 'josh@example.com', password: 'passw0rd', password_confirmation: 'passw0rd')
{% endhighlight %}

Lastly, we’re going to migrate our changes to the database and then seed it with our test user. In your terminal, run the commands below.

{% highlight bash %}
rake db:migrate
rake db:seed
{% endhighlight %}

Let’s test our changes. In your terminal run the ```rails console``` commands to load our application. Then, enter the following statement: ```User.first```. This will query the database and return the first user found. If the application is working properly, the user found should be the user that you used to seed the database.

## Step 3: The Controller

With the models finished, we can now setup the controller. In this step, we’re going to create a ```UsersSessionsController``` that will be used to allow users to sign in and out of the application.

To generate the ```UserSessionsController```, open your terminal and run the command below.

{% highlight bash %}
rails generate controller UserSessions
{% endhighlight %}

Our controller will have four actions. Two will allow users to sign in, one will allow users to sign out, and one will show information about the user that’s currently signed in.

In your text editor, modify the ```UserSessionsController``` to match the example below.

{% highlight ruby %}
# app/controllers/user_sessions_controller.rb
class UserSessionsController < ApplicationController
  def new
    @user_session = UserSession.new
  end

  def create
    @user_session = UserSession.new(params[:user_session])
    if @user_session.save
      flash[:notice] = "Sign in successful!"
      redirect_to user_session_url
    else
      render 'new'
    end
  end

  def show
    user_session  = UserSession.find
    @current_user = user_session.user
  end

  def destroy
    user_session = UserSession.find
    user_session.destroy
    redirect_to root_url
  end
end
{% endhighlight %}

Next, we’re going to create two views. The new view will display our sign in form and the show view will be what the user sees when they sign in.

In your text editor, create the new and show views as shown below.

{% highlight erb %}
<!-- app/views/user_sessions/new.html.erb -->
<h2>Sign In</h2>

<% if @user_session.errors.any? %>
  <div id="error-explanation">
    <h3>The following errors occurred:</h3>
    <ul>
      <% @user_session.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
    </ul>
  </div>
<% end %>
{% endhighlight %}

{% highlight erb %}
<%= form_for @user_session, url: user_session_path do |f| %>
  <p>
    <%= f.label :email %><br/>
    <%= f.text_field :email %>
  </p>
  <p>
    <%= f.label :password %><br/>
    <%= f.password_field :password %>
  </p>

  <%= f.submit 'Sign In' %>
<% end %>
{% endhighlight %}

{% highlight erb %}
<!-- app/views/user_sessions/show.html.erb -->
<h2>Your Session</h2>
<p>
  <%= flash[:notice] %>
</p>
<p>
  You're signed in as: <b><%= @current_user.email %></b><br/>
  <%= link_to 'Sign Out', user_session_path, method: 'delete' %>
</p>
{% endhighlight %}

Finally, we need to add the controller as a resource in our routes file. In your text editor, modify the routes files to match the example below.

{% highlight ruby %}
# config/routes.rb
resource :user_session
root to: 'user_session#new'
{% endhighlight %}

Let’s test our changes. In your terminal, run the ```rails server``` command. Then, open your browser and navigate to ```http://localhost:3000```. You should see a login form. If the application is working properly you should be able to sign in with the test user that you created in the previous step.

## Step 4: Tests

This final step will demonstrate how to test with Authlogic. We’re going to create a user to test with and a functional test that will verify that users can sign in and out of the application.

First, let’s create the test user. In your text editor, modify the users fixture to match the example below.

{% highlight yaml %}
# test/fixtures/users.yml
josh:
  email: josh@example.com
  password_salt: <%= salt = Authlogic::Random.hex_token %>
  crypted_password: <%= Authlogic::CryptoProviders::Sha512.encrypt('passw0rd' + salt) %>
  persistence_token: <%= Authlogic::Random.hex_token %>
{% endhighlight %}

Now, let’s write the tests. In your text editor, modify the ```UserSessionsControllerTest``` to match the example below.

{% highlight ruby %}
# test/functional/user_sessions_controller_test.rb
require 'test_helper'
require 'authlogic/test_case'

class UserSessionsControllerTest < ActionController::TestCase
  setup :activate_authlogic

  test 'it should sign a user in on create' do
    post :create, user_session: {
      email: 'josh@example.com',
      password: 'passw0rd',
      password_confirmation: 'passw0rd'
    }

    assert_equal 'Sign in successful!', flash[:notice]
    assert_redirected_to home_url
  end

  test 'it should sign a user out on destroy' do
    UserSession.create(users(:josh))
    delete :destroy
    assert_nil UserSession.find
    assert_redirected_to root_url
  end
end
{% endhighlight %}

Let’s test our changes. In your terminal, run the ```rake test``` command. If the tests are setup properly, both should pass.

## Conclusion

Congratulations! You should be up and running with Authlogic. For more information about Authlogic, [check out the project on GitHub][1]. For a more in-depth example, [check out the example application][2].

[1]: http://github.com/binarylogic/authlogic
[2]: http://github.com/binarylogic/authlogic_example
