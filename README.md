# Deploy Github repos to Heroku

A few hk plugins that let you deploy directly from Github to Heroku.

Assumptions:

* You're using Github as your main collaborative space for development
* You've got Heroku set up and you're using the new [hk CLI](https://github.com/heroku/hk)
* You're in a project directory with the 'origin' remote pointing to your Github repo

# Install

    $ git clone https://github.com/jesperfj/hk-gh.git
    $ cd hk-gh
    $ ./install

# Usage

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
# deploy
$ hk deploy staging
```

## Deploy

```
$ hk deploy staging
Deploying ece5bdc4d6b495e1bd63ad87c1480700361e8dc6 on branch staging to app acme-staging
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

