# Finance Tracker Rails App

Finance Tracker is a stock tracker app that allows users to track up to 10 stocks each.  There is an authentication
system that allows users to create accounts, login, logout, and change password.  Users will be able to search for other
users by email or stock ticker.  More updates to follow.

## Getting Started

These instructions will allow you to make a copy of the project yourself, and get it up and running on your local 
machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites

RubyMine 2020.2, Ruby 2.7.1, and Rails 6.0.3.3 were used to initially make this project on a machine with SQLite.  I try
to keep the application up to date so if the latest commit doesn't work try going back to an earlier one.

## FOR REFERENCE (Step by step building of application):

### Setup Application for use with Heroku

Update Gemfile to handle PostgreSQL in Heroku:

```ruby
group :production do
  gem 'pg'
end
```

### Initial Controller and Root Route

Generate a new controller called welcome with the action index:

```shell
rails g controller welcome index
```

Update config > routes.rb with root path directing to welcome#index:

```ruby
root 'welcome#index'
```

### Setup Authentication

Add Devise Gem to Gemfile:

```ruby
gem 'devise'
```

Install Devise in Rails:

```shell
rails generate devise:install
```

Create User Model with Devise:

```shell
rails generate devise User
```

Force all Users of the Application to Authenticate (add to ApplicationController):

```ruby
before_action :authenticate_user!
```

Handle Flash Messages in app > views > layouts > application.html.erb:

```html
<p class="notice"><%= notice %></p>
<p class="alert"><%= alert %></p>
```

Add Logout Functionality to Welcome#Index:

```shell
<%= link_to 'Sign Out', destroy_user_session_path, method: :delte %>
```

### Setup Bootstrap

Install Bootstrap, jquery, and popper.js via yarn:

```shell
yarn add bootstrap jquery popper.js
```

Update config > webpack > environment.js so Rails understands jquery and popper.js syntax:

```javascript
const { environment } = require('@rails/webpacker')

const webpack = require("webpack")

environment.plugins.append("Provide", new webpack.ProvidePlugin({
$: 'jquery',
jQuery: 'jquery',
Popper: ['popper.js', 'default']
}))

module.exports = environment
```

Import bootstrap into app > javascript > packs > application.js:

```javascript
import "bootstrap"
```

Update app > assets > stylesheets > application.css to use Bootstrap:

```css
*= require bootstrap
```

Update views with devise-bootstrap-views gem in Gemfile:

```ruby
# Add devise-bootstrap-views Gem
gem 'devise-bootstrap-views', '~> 1.0'
```
```shell
rails generate devise:views:bootstrap_templates
```


## Running the tests

Tests to come at a later date.  Want to write some?

## Deployment

Should easily deploy to Heroku.  Instructions for that at a later date if needed.

## Built With

* [Ruby](https://www.ruby-lang.org/en/) - Language
* [Ruby on Rails](https://rubyonrails.org) - MVC Framework
* [RubyMine](https://www.jetbrains.com/ruby/) - IDE
* [PostgreSQL](https://www.postgresql.org) - Database

## Contributing

If you want to ...

## Authors

* **Jeremy Hastings** - *Initial work* - [Jeremy Hastings](https://github.com/jeremyhastings/)

## License

This project is licensed under the GNU General Public License 3.0 License - see the [LICENSE.md](LICENSE.md) file for details
