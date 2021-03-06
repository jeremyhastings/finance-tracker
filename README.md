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
<%= link_to 'Sign Out', destroy_user_session_path, method: :delete %>
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

### Add IEX Cloud Stock API

Add gem to gemfile:

```ruby
# Add IEX Cloud Ruby Client Gem for Stock Data API
gem 'iex-ruby-client'
```

Create Stock Model:

```shell
rails g model ticker name last_price:decimal
```

```shell
rails db:migrate
```

Create new_lookup method in Stock Model:

```ruby
def self.new_lookup(ticker_symbol)
  client = IEX::Api::Client.new(
    publishable_token: ENV['IEX_API_PUBLISHABLE_TOKEN'],
    endpoint: 'https://sandbox.iexapis.com/v1'
  )
  # client.price(ticker_symbol)
    begin
      new(ticker: ticker_symbol, name: client.company(ticker_symbol).company_name, last_price: client.price(ticker_symbol))
    rescue => exception
      return nil
    end
end
```

Secure Credentials in credentials.yml.enc (Using VS Code):

```shell
EDITOR="code --wait" rails credentials:edit
```

```text
iex_client:
  sandbox_api_key: "ENTER PUBLISHABLE KEY HERE"
```

### Setup Front-End for Stock Portfolios

Add 'my_portfolio' route to routes.rb:

```ruby
get 'my_portfolio', to: 'users#my_portfolio'
```

Create Controller and Views for my_portfolio:

```shell
rails g controller Users my_portfolio
```

Add Font Awesome Gem:

```ruby
# Add Font Awesome to Application
gem 'font-awesome-sass', '~> 5.13.0'
```

Add search_stock to routes:

```ruby
get 'search_stock', to: 'stocks#search'
```

Create stocks_controller.rb with search method:

```ruby
class StocksController < ApplicationController
  def search
    if params[:stock].present?
      @stock = Stock.new_lookup(params[:stock])
      if @stock
        respond_to do | format |
          format.js { render partial: 'users/result' }
        end
      else
        respond_to do | format |
          flash.now[:alert] = "Please enter a valid symbol to search"
          format.js { render partial: 'users/result'}
        end
      end
    else
      respond_to do | format |
        flash.now[:alert] = "Please enter a symbol to search"
        format.js { render partial: 'users/result'}
      end
    end
  end
end
```

### Add JavaScript to handle AJAX request (remote: true):

Create _result.js.erb and add JavaScript:

```javascript
document.querySelector('#results').innerHTML = '<%= j render 'users/result.html' %>'
```

### Add Message partial to result partial to handle invalid searches:

```erbruby
  <%= render 'shared/messages' %>
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
* [Bootstrap](https://getbootstrap.com) - UI Framework
* [Devise](https://github.com/heartcombo/devise) - Authentication
* [Devise Bootstrap Views](https://github.com/hisea/devise-bootstrap-views) - UI Templates for Devise
* [IEX Cloud](https://iexcloud.io/) - Stock Ticker API
* [IEX Cloud Ruby Gem](https://github.com/dblock/iex-ruby-client) - IEX Cloud API Wrapper

## Contributing

If you want to ...

## Authors

* **Jeremy Hastings** - *Initial work* - [Jeremy Hastings](https://github.com/jeremyhastings/)

## License

This project is licensed under the GNU General Public License 3.0 License - see the [LICENSE.md](LICENSE.md) file for details
