# QUICK SETUP

- copy `.env.example` to `.env` and adjust as needed
- run `bundle install`
- install docker
- run `docker-compose up pg96 adminer redis es maildev`
- run `bundle exec rake db:create db:extensions db:schema:load db:migrate db:seed`
- run `foreman start -c job=1,web=1,rpush=1`
- start developing

## GraphQL
- run `./dumpschema <path to>/TipHiveJS/schema.graphql.json` if you make graphql changes 
  and commit the newly generated `schema.graphql.json` on `TipHiveJS` repo.


## Push Notifications
- run `bundle exec rake rpush:fcm_webpush PUBLIC_KEY=<public_key> PRIVATE_KEY=<private_key>`
  where `public_key` and `private_key` are Firebase Cloud Messaging - Web Push Certificates keys.

## Important Information
- Documentation links are found below

### Getting started with TipHiveAPI
#### These are the software packages we use
- Ruby 2.5.5
- Rails 5.2.6
- Postgres 9.6+
- Sidekiq Gem for managing jobs
- Redis for storing jobs
- Rubocop for ensuring code quality
- Rspec for testing framework

#### Optional
- Overcommit - overcommit -install   <--- ensures code quality on commit
- You may run Guard to automatically watch files

### Installing
#### Installing on Ubuntu Linux
- I recommend this: https://gorails.com/setup/ubuntu/16.04
  + Make sure you select the ubuntu version you are using
  + Which sections to follow:
    * Installing Ruby (I recommend rvm)
    * Configuring Git, you probably already have this :)
    * Skip installing Rails for now, we'll come back to this
    * Setting up PostgreSQL
- Install Redis
  + sudo apt-get install redis-server
- git clone the repo
- Now, skip to POST-INSTALL section

#### Installing on a Mac
*These instructions are using Homebrew for certain things*
*If you prefer not to use Homebrew, adjust accordingly*
###### Homebrew
- If you do not have homebrew, Install homebrew - http://brew.sh/
- If you already have homebrew, or don't know
  + run `brew update` and `brew doctor` to make sure your system is still ready to brew.
  + To upgrade your existing packages, run `brew upgrade`.

###### Postgres
- We use version 9.6, I have not tried other versions
- If you do not have postgres, use homebrew to install `brew install postgres`
  + Once this command is finished, it gives you a couple commands to run. Follow the instructions and run them:

      ```
      # To have launchd start postgresql at login:
      ln -sfv /usr/local/opt/postgresql/*plist ~/Library/LaunchAgents

      # Then to load postgresql now:
      launchctl load ~/Library/LaunchAgents/homebrew.mxcl.postgresql.plist
      ```

  + By default the postgresql user is your current OS X username with no password. For example, my OS X user is named chris so I can login to postgresql with that username.

###### Redis
- `brew install redis`

###### Ruby
*We use RVM*
- If you don't have RVM installed, install it: https://rvm.io/rvm/install
  + Try first without any gpg operations to see if it will work
  + **NOTE: Only install RVM, which is the first option after the gpg instructions**
- Make sure to follow any instructions the installer may give at the end
- Once installed, and before you install other versions of ruby in the future
  + Run `rvm get stable --autolibs=enable --auto-dotfiles`
- Install our version of ruby: `rvm install 2.3.5`
  + Not necessary, but if you *want* 2.3.5 to be the default: `rvm use 2.3.5 --default`
  + run `ruby -v` to make sure the running ruby matches

### POST-INSTALL
###### Gemset and Rails
- You'll need a gemset for our project.
  + `rvm use 2.3.5`
  + `rvm gemset create tiphive-api`
- Install bundler in the global gemset:
  + make sure you're using 2.3.5 - `rvm list`
  + run `rvm gemset use global`
  + run `gem install bundler`

###### API Setup
- Clone our API repo to the location of your choice
- `cd` into the API project directory
- Check that the ruby and gemset was selected
  + `rvm list` should show that 2.3.5 is active
  + `rvm gemset list` should show that the tiphive-api gemset is active
    * If not, `rvm gemset use tiphive-api`
- run `bundle install` to install our gems, and follow any instructions that appear

###### Database setup
- Create a `.env` file in the project root
- The contents of .env should be

```
# Placeholders have brackets [placeholder]

FOG_PROVIDER=AWS
FOG_AWS_KEY=[KEY HERE]
FOG_AWS_SECRET=[SECRET KEY HERE]

FOG_AWS_REGION=[REGION HERE]
FOG_AWS_BUCKET=[BUCKET HERE]

DEVISE_PEPPER=[DEVISE PEPPER HERE]

FRONTEND_URL=localhost:3000
TIPHIVE_HOST=[example tiphive.dev]
TIPHIVE_EMAIL_ADMIN=tiphive@tiphive.com
TIPHIVE_HOST_NAME=[example tiphive.dev]

DEVELOPMENT_DATABASE=TipHiveAPI_development
DEVELOPMENT_DATABASE_PASSWORD=

EDITOR_EMAIL=daniel+tiphiveeditor@tiphive.com

REDIS_HOST=localhost
REDIS_PORT=6379

SUPPORT_DOMAIN_ID=[after migrating database, put support domain ID here]
SUPPORT_USER_ID=[Create a support user and put ID here]

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
```

- replace all the placeholders in the .env file with real values
  + I can provide secret keys as needed, just contact me

#### NOTE: IMPORTANT
- You must run the following in order only on initial start
  - `bundle install`
  - `bundle exec rake db:create`
    + If you get a `fe_sendauth: no password supplied` error
      * Change your pg_hba.conf file as outlined here:
  - `bundle exec rake db:extensions`    <--- Must be run before migrate
  - `bundle exec rake db:migrate`

###### API Up and Running
- `foreman start` should start redis, sidekiq and the web
- Run tests, everything should pass

##### Description
- The API uses the following
  + rails-api gem
  + active_model_serializers
    * https://github.com/rails-api/active_model_serializers
  + JSONAPI format
  + Multi-tenancy through the Apartment gem
    * https://github.com/influitive/apartment
    * Importantly, please read [Multi-tenancy](docs/multi_tenancy.md)
- Articles explaining the direction we are taking
  + http://fancypixel.github.io/blog/2015/01/28/react-plus-flux-backed-by-rails-api/
  + http://code-worrier.com/blog/custom-slugs-in-rails/

## TipHive Documentation
- [API Docs](docs/api_v2_docs.md)
- [Multi-tenancy](docs/multi_tenancy.md)
- [Gem Information](docs/gems.md)

#### Conventions
##### Model
- The order of things
  + includes
  + acts_as_...
  + associations
  + validations
  + callbacks
  + scopes
  + searchable block if any
  + instance methods
  + class methods

#### Things to look into
- Using cascading deletes on the database instead of dependendent: delete
- adding foreign keys everywhere
- using paper_trail for versioning and soft-delete
- check for strong_parameters outside of a controller if we do things like retreive data from an ouside source: https://github.com/rails/strong_parameters
