# Deploy Github repos to Heroku

A few [hk][1] plugins that let you deploy directly from Github to Heroku. 

This is a working prototype. The purpose is to demonstrate this kind of workflow.

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

Start by logging into Github:

```
$ hk gh-login
Github username: roanak
Github password (never stored): 
2-factor code (leave empty if not using 2 factor): 123456
Authenticating... done
Access token stored in /Users/roanak/.heroku/github
```

## Bootstrapping from sample code

Nobody writes code from scratch anymore. When you decide to try out a new, promising framework or library, it comes with sample code that you can copy and run to get off the ground quickly. From there you will make the code your own, but it's a huge time saver to get some help with the basics.

When you've found a great sample app on Github which includes an app.json file you can bootstrap your own Heroku app from the sample in a few simple steps:

### Fork the app

Github makes this easy. For example, go ahead and [fork jesperfj/node-todo](https://github.com/jesperfj/node-todo/fork) (which is itself a fork of [scotch-io/node-todo](https://github.com/scotch-io/node-todo))

### Clone locally

Now clone the code down to your personal development environment with:

    $ git clone git@github.com:roanak/node-todo.git

(replace roanak with your own Github username).

### Set up app on Heroku

Thanks to app.json and Heroku's new setup service, you can bootstrap the app on Heroku as simple as:

    $ hk setup roanak-todo
    Setting up new Heroku app for branch master in roanak/node-todo
    Starting setup... done
      (haiku app = boiling-mesa-6179, setup id = a237100b-7019-4b7f-80f1-2db6bde31d90)
    Deploying and running scripts........ done
    Renamed app to roanak-todo

It's optional (but recommended) to provide an app name when you run setup on the master branch. This establishes and meaningful project name on Heroku. If you don't provide one, your app will get a haiku name like boiling-mesa-6179.

In less than a minute you went from discovering a new interesting sample app to running it in your own dev environment, ready for hacking!

### Deploy a change

Deploying your own changes is dead simple from here. It follows the standard git workflow:

1. Make change locally
2. `git add` and `git commit`
3. `git push origin master`

Once your change is on Github, you can deploy with:

    $ hk deploy
    Deploying e2a40d9411 from master in roanak/node-todo to roanak-todo
    Starting build... done
    Build id: 33726cf9-594c-4c21-bf72-46b1ebbc2733
    Building... done

    -----> Node.js app detected
    -----> Requested node range: 0.10.x
    -----> Resolved node version: 0.10.28
    -----> Downloading and installing node
    -----> Restoring node_modules directory from cache
    -----> Pruning cached dependencies not specified in package.json
    -----> Writing a custom .npmrc to circumvent npm bugs
    -----> Exporting config vars to environment
    -----> Installing dependencies
    -----> Caching node_modules directory for future builds
    -----> Cleaning up node-gyp and npm artifacts
    -----> No Procfile found; Adding npm start to new Procfile
    -----> Building runtime environment
    -----> Discovering process types
     Procfile declares types -> web

    -----> Compressing... done, 9.4MB
    -----> Launching... done, v5
     http://roanak-todo.herokuapp.com/ deployed to Heroku

## Team development with branch deployments

TODO.
