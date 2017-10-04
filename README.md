# Re-former Project

The goal of this project is to create forms in Rails using HTML, `form_tag`, and `form_for`.

This is a project from [The Odin Project](https://www.theodinproject.com/courses/ruby-on-rails/lessons/forms).

## Pre-Project Thoughts

Not any.

## Post-Project Thoughts

I've taken in a lot of information about Rails and the difficulty going forward appears to be linking all the concepts together.

## Reflection (for Comprehension)

1. First, the Model was created using `rails generate model User username:string password:string email:string`, which creates the appropriate migration in /app/db/migrate.

2. Then, the table is created using `rails db:migrate`.

3. Now, that the Model has been created, it is time to create the User using `rails generate controller User new create`. For the purposes of this project, the routes are edited to use the `:resources` shortcut but with `only: [:new, :create]`. I also edit the model itself in /app/models/user.rb to validate presence, uniqueness, and length of all three parameters.

4. As such, there are now empty `new` and `create` methods in the UserController, as well as `new.html.erb` and `create.html.erb` files in /app/views/users, so I delete the `create` file accordingly.

5. Next, I create the HTML version of the form:

```erb
<form action="/users" method="post" accept-charset="UTF-8">
	<label for="user[email]">Email:</label>
	<input type="text" name="user[email]"><br>
	<label for="user[username]">Username:</label>
	<input type="text" name="user[username]"><br>
	<label for="user[password]">Password:</label>
	<input type="password" name="user[password]"><br>
	<input type="submit">
	<input type="hidden" name="authenticity_token" value="<%= form_authenticity_token %>">
</form>
```

	1. To replicate the way Rails generates forms, I have added the `accept-charset` attribute, and nested whatever the `for` attribute receives.

	2. Furthermore, a CSRF token is not automatically created by Rails, so I created an authenticity token in a hidden input that used the Rails method `form_authenticity_token`.

6. To match the form data, I fill out the user#create method and create the private #user_params method to require/permit the appropriate fields since we are passing Rails a hash.

```
def create
  @user = User.new(user_params)
  if @user.save
    redirect_to new_user_path
  else
    render :new
  end
end

private
def user_params
  params.require(:user).permit(:username, :email, :password)
end
```

	1. Right now, when the users/new page is visited, the browser is sent to the new.html page rendered via the new.html.erb file. When the form is filled out and submitted, the params hash consists of another hash that has the keys of email, username, and password. The user_params method requires the user hash and permits all three of these keys and returns the filtered hash. 

	2. The filtered hash is passed to the User.new method call which creates a new user and saves it in @user.

	3. Then Rails tries to save the user to the database. If the new user meets the validations created in the Model, then it renders the new page again, letting you create another user. Normally, this would probably display the user created, but for the purposes of this project, this was intentional.

	4. If Rails fails to save the user to the database, it redirects the page to the new_user_path which is the new.html page again, allowing one to try again.

7. Next, we removed the HTML version and switched to the form_tag version.

```erb
<%= form_tag('/users/') do %>
	<%= label_tag(:email, "Email:") %><br>
	<%= text_field_tag(:email) %><br>
	<%= label_tag(:username, "Username:") %><br>
	<%= text_field_tag(:username) %><br>
	<%= label_tag(:password, "Password:") %><br>
	<%= password_field_tag(:password) %><br>
	<%= submit_tag("Submit") %>
<% end %>
```

	1. In this case, we added `@user = User.new` to the user#new action, because the Rails form helper methods are smart enough to populate the form if the object already exists.

	2. The form_tag helper requires one to use a variety of input tags to produce the same output.

8. Then, we switched to form_for. 

```erb
<%= form_for @user do |f| %>
	<%= f.label :email %><br>
	<%= f.text_field :email, :value => "example@example.com" %><br>
	<%= f.label :username %><br>
	<%= f.text_field :username %><br>
	<%= f.label :password %><br>
	<%= f.password_field :password %><br>
	<%= f.submit "Submit" %>
<% end %>
```

	1. form_for automatically nests the params attributes.

	2. form_for accepts an instance of the Model as an argument and it knows what to do.

9. Then we created the users#edit action. To enable this, we had to add the routes of edit and update to the /config/routes.rb file in order for all the necessary paths to be created.

10. I just copy and pasted the form and added in default values.