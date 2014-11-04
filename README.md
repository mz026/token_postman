# WARNING:
This gem is renamed to [whoru](https://github.com/mz026/whoru) and no longer maintained.
Please refer to the [new place](https://github.com/mz026/whoru) for updated codes.

# TokenPostman

A Rails plugin handling login stuff in Controller

## Installation

add `gem 'token_postman'` in the `Gemfile` and then `$ bundle install`

## Usage

in `users_controller.rb`
```ruby
class UsersController < ApplicationController
  include TokenPostman
  login FacebookUser, :with => :facebook_login
end
```

in `config/routes.rb`
```ruby
post '/fb_login' => 'users#facebook_login'
```

## In a nutshell:
After the setup above, the `/fb_login` api would take 

```json
{
  "account": "the-account",
  "validator": "the-validator"
}
```

- `200`: When success, returns `user.as_json` 
- `409`: When login failed, returns `409`
- `400`: Returns `400` if miss parameters

### App Login:
Append `access_token` onto response.

### Web Login:
TokenPostman does web login if `params[:cookie]` exists.
When web login, TokenPostman appends `access_token` on `cookies['TOKEN_POSTMAN']`, instead of appending it on response.


## How it works:
Take the setup above as an example:

TokenPostman create a method `facebook_login` on the fly.
In the created method, TokenPostman takes `params[:account]` and `params[:validator]` 
and pass them into `FacebookUser::login(account, validator)`, which should return a user.

TokenPostman then invokes `user#generate_access_token`(`user#generate_web_access_token` for web login) and appends it on response / cookies.
It would raise `TokenPostman::MethodNotImplementedException` if any one of `FacebookUser::login`, `FacebookUser#generate_access_token`, `FacebookUser#generate_web_access_token` is not implemented.


## Model Requirement

- `UserClass::login(account, validator)`: 
should return a user if account and valiator are matched, return `nil` if not.

- `UserClass#generate_access_token`: 
should return an access token used by app login

- `UserClass#generate_web_access_token`:
should return an access for web login


## Licence: 
MIT
