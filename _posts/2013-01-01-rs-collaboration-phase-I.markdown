---
layout: post
title:  "Phase I - RS Collaboration Development"
date:   2014-01-01 19:54:50
categories: 
---

### ToDo

- Create the APP
- Setup Devise w/ Google OmniAuth

#### Create the Application

* Initialize a new App with a postgres DB and no test units. (Will use RSPEC for testing)

```bash
rails new rs_collaboration --database=postgresql --skip-test-unit
```

* Remove the Username and Password fields from "database.yml"


#### Git

* Add to '.gitignore' file

```
# Compiled source #
###################
*.com
*.class
*.dll
*.exe
*.o
*.so

# Packages #
############
# it's better to unpack these files and commit the raw source
# git has its own built in compression methods
*.7z
*.dmg
*.gz
*.iso
*.jar
*.rar
*.tar
*.zip

# Logs and databases #
######################
*.log
*.sql
*.sqlite

# OS generated files #
######################
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db
```

* Initialize your Repository

```ruby
git init
git add .
git commit -m "first commit"
```

#### Create the Root 

```ruby
rails g controller Collaboration index
```

#### Security

* Add the following three Gems

```ruby
gem 'devise'
gem 'omniauth-google-oauth2'
gem 'figaro'
```

* bundle install

##### Google Login

```ruby
rails generate figaro:install
```

https://cloud.google.com/console?redirected=true#/project/apps~refocus-studios/apiui/credential

* Ensure that your redirect URI is set appropiately
* Grab the 'client ID' and 'client Secret' variables and add to the 'config/application.yml'

```ruby
GOOGLE_CLIENT_ID: "Add here"
GOOGLE_CLIENT_SECRET: "Add here"
```

##### Devise

```ruby
rails generate devise:install
rails generate devise User
rake db:migrate
```

* Edit the 'User' model and remove the following items. 

['registrable', 'recoverable' and 'validatable']

* Add the following to your 'config/initializers/devise.rb' file.

```ruby
require "omniauth-google-oauth2"
config.omniauth :google_oauth2, ENV["GOOGLE_CLIENT_ID"], ENV["GOOGLE_CLIENT_SECRET"], { access_type: "offline", approval_prompt: "" }
```

* Add the following to your 'app/models/user.rb' file

```ruby
devise :omniauthable, :omniauth_providers => [:google_oauth2]
```

* Add the following to your 'routes' file

```ruby
devise_for :users, :controllers => { :omniauth_callbacks => "users/omniauth_callbacks" }
```

* Create a new file 'app/controllers/users/omniauth_callbacks_controller.rb' and add the following

```ruby
class Users::OmniauthCallbacksController < Devise::OmniauthCallbacksController
  def google_oauth2
      # You need to implement the method below in your model (e.g. app/models/user.rb)
      @user = User.find_for_google_oauth2(request.env["omniauth.auth"], current_user)

      if @user
        flash[:notice] = I18n.t "devise.omniauth_callbacks.success", :kind => "Google"
        sign_in_and_redirect @user, :event => :authentication
      else
				flash[:notice] = "Login Failed"
        redirect_to root_path
      end
  end
end

```

* Add the following to your "user.rb" model

```ruby
def self.find_for_google_oauth2(access_token, signed_in_resource=nil)
    data = access_token.info
    user = User.where(:email => data["email"]).first
    user
end
```

* Manually add users using the Rails Console

```
User.create( email: "ari@refocus-studios.com", password: Devise.friendly_token[0,20])
```

* Add the following to your 'index.html' page and test it.

```
<% unless user_signed_in? %>
<%= link_to "Sign in with Google", user_omniauth_authorize_path(:google_oauth2) %>
<% else %>
<%= link_to'Logout', destroy_user_session_path, :method => :delete %>   
<% end %>
```

(i) [Devise Documentation](https://github.com/plataformatec/devise)

(i) [Google OmniAuth2 Gem](https://github.com/plataformatec/devise/wiki/OmniAuth:-Overview#google-oauth2-example)


=======
### Phase II

- Add Bootstrap
- Push to Heroku

#### Bootstrap

* [Physically added Bootstrap files to the project.](http://getbootstrap.com/)



* [Added alerts that work with Bootstrap](https://gist.github.com/ariperez83/8208993)
 


* Added a Navbar

#### Heroku

* Set the following settings in the '/config/environments/production' file so that Bootstrap will work on Heroku.

```ruby
config.cache_classes = true
config.serve_static_assets = true
config.assets.compile = true
config.assets.digest = true
```

* Set the Heroku Google OmniAuth2 variables

```bash
rake figaro:heroku
```
* Manually add users using the Heroku Rails Console

```
heroku run rails c
```

```
User.create( email: "ari@refocus-studios.com", password: Devise.friendly_token[0,20])
```

