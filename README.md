# Deploy Github repos to Heroku

A few [hk][1] plugins that let you deploy directly from Github to Heroku.

Assumptions:

* You're using Github as your main collaborative space for development
* You've got Heroku set up and you're using the new [hk CLI][1]
* You're in a project directory with the 'origin' remote pointing to your Github repo
* You have a _recent version_ of [jq][2] installed. The scripts make use of the `reduce` function which is fairly new.

[1]: https://github.com/heroku/hk
[2]: http://stedolan.github.io/jq/

# Install

    $ git clone https://github.com/jesperfj/hk-gh.git
    $ cd hk-gh
    $ ./install

# Usage

NOTE: README needs to be updated to document new setup and target commands.

## Point branches at apps

First step is to map your branches to Heroku apps. For example point `master` to your production app: `acme-prod`:

```
$ git config branch.master.heroku acme-prod
```

Point your staging branch to a staging app:

```
$ git config branch.staging.heroku acme-staging
```

## Log into Github

```
$ hk gh-login
Github username: joe
Github password (never stored): 
2-factor code (leave empty if not using 2 factor): 123456
Authenticating... done
Access token stored in /Users/joe/.heroku/github
```

## Write and push code

Work on staging and push:

```
$ git fetch origin
$ git checkout staging
# write code and commit
$ git add . && git commit
# push to staging on github
$ git push origin staging
```

## Deploy

```
$ hk deploy staging
Deploying ece5bdc4d6 from staging on acme-inc/acme to acme-staging
Starting build... done
Build ID: 1234abcd-1234-abcd-abef-00c0b657cf9a
Building........ done

-----> Node.js app detected
-----> Defaulting to latest stable node: 0.10.28
-----> Downloading and installing node
-----> Restoring node_modules directory from cache
-----> Pruning cached dependencies not specified in package.json
-----> Writing a custom .npmrc to circumvent npm bugs
-----> Installing dependencies
-----> Caching node_modules directory for future builds
-----> Cleaning up node-gyp and npm artifacts
-----> No Procfile found; Adding npm start to new Procfile
-----> Building runtime environment
-----> Discovering process types
 Procfile declares types -> web

-----> Compressing... done, 5.8MB
-----> Launching... done, v24
 http://acme-staging.herokuapp.com/ deployed to Heroku
```

## Deploy to production

If you like your changes, merge them into master and push to Github:

```
$ git checkout master
$ git merge staging
$ git push origin master
```

master is the default deployment branch, so you can deploy to production with

```
$ hk deploy
```

# Show builds

Check out past builds with:

```
$ hk show-build
Builds on acme-staging:
db827346-929e-4859-9ef7-ec52067e50d6  succeeded joe@acme.com 2014-05-02T22:21:28+00:00
d312373f-8f12-4fb8-8d6e-1d1cba3df3ad  succeeded joe@acme.com 2014-05-02T22:05:03+00:00
e123120e-85ce-42fb-bc12-561f1e0da6b4  succeeded joe@acme.com 2014-05-02T21:50:31+00:00
```

See the details and build output of a particular build:

```
$ hk show-build db827346-929e-4859-9ef7-ec52067e50d6
App:        acme-staging
Build id:   db827346-929e-4859-9ef7-ec52067e50d6
Created at: 2014-05-02T22:21:28+00:00
Version:    e5c4387c08c4c66defafd31ada8f476509fe1061
User:       joe@acme.com
Status:     succeeded
Slug id:    2a284726-786e-4472-b2dc-1fcea294f0ab
Build output:

-----> Node.js app detected
-----> Defaulting to latest stable node: 0.10.26
-----> Downloading and installing node
-----> Restoring node_modules directory from cache
-----> Pruning cached dependencies not specified in package.json
-----> Writing a custom .npmrc to circumvent npm bugs
-----> Installing dependencies
-----> Caching node_modules directory for future builds
-----> Cleaning up node-gyp and npm artifacts
-----> No Procfile found; Adding npm start to new Procfile
-----> Building runtime environment
-----> Discovering process types
       Procfile declares types -> web

-----> Compressing... done, 9.3MB
-----> Launching... done, v6
       http://acme-staging.herokuapp.com/ deployed to Heroku
```

