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

Team development processes vary from place to place. This set of plugins are designed for a particular process used for web app development by many organizations and [documented by Github back in 2011](http://scottchacon.com/2011/08/31/github-flow.html):

* Anything in the master branch is deployable
* To work on something new, create a descriptively named branch off of master (ie: new-oauth2-scopes)
* Commit to that branch locally and regularly push your work to the same named branch on the server
* When you need feedback or help, or you think the branch is ready for merging, open a pull request
* After someone else has reviewed and signed off on the feature, you can merge it into master
* Once it is merged and pushed to ‘master’, you can and should deploy immediately

(I don't claim that Github invented it. But they've been a champion for this style of development and we use it too at Heroku).

You just got your project off the ground and now it's time to write code.

### Set up topic branch with its own Heroku app

Create local branch:

    $ git checkout -b new-feature
    Switched to a new branch 'new-feature'

Write some code, then commit and push branch to origin

    $ git add . && git commit -m "some code"
    ...
    $ git push origin new-feature 
    Total 0 (delta 0), reused 0 (delta 0)
    To git@github.com:roanak/node-todo.git
     * [new branch]      new-feature -> new-feature

Set up a Heroku app for this topic branch:

    $ hk setup
    Setting up new Heroku app for branch new-feature in roanak/node-todo
    Starting setup... done
      (haiku app = shrouded-wildwood-2949, setup id = f4b3527a-3a58-4d10-95a2-002e5afdd6fa)
    Deploying and running scripts....... done
    Post deploy script output:

    Renaming from shrouded-wildwood-2949 to roanak-todo-new-feature...
    Renamed app to roanak-todo-new-feature

That's it. You have your very own Heroku deployment for this topic branch. Check it out:

    $ hk open -a roanak-todo-new-feature

You can see how your branches are targeted to different Heroku apps with:

    $ hk target
    master       ->  roanak-todo
    new-feature  ->  roanak-todo-new-feature

### Deploying new code from the topic branch

The `setup` command performs first time setup, creating the app and provisioning add-ons, etc. For deploying new changes, use the `deploy` command. For example, if you've made some code edits, you follow the standard git workflo:

    $ git add . && git commit -m "more code"
    $ git push origin new-feature
    ...

Then run:

    $ hk deploy
    Deploying b408672096 from new-feature in roanak/node-todo to roanak-todo-new-feature
    Starting build... done
    Build id: 8d1fdedd-1613-474e-2940-a2a10d4748c1
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

    -----> Compressing... done, 9.3MB
    -----> Launching... done, v5
     http://roanak-todo-new-feature.herokuapp.com/ deployed to Heroku

## Viewing past build outputs

The build status and output for each build can be viewed later. Get a list of past builds with:

    $ hk show-build
    Builds on roanak-todo-new-feature:
    4358645d-4c75-2ef4-a6d0-41c460935dec  succeeded roanak@isfjeldet.io 2014-05-06T16:42:58+00:00
    8d1fdedd-1613-474e-2940-a2a10d4748c1  succeeded roanak@isfjeldet.io 2014-05-06T16:50:26+00:00

View a build with:

    $ hk show-build 8d1fdedd-1613-474e-2940-a2a10d4748c1
    App:        roanak-todo-new-feature
    Build id:   8d1fdedd-1613-474e-2940-a2a10d4748c1
    Created at: 2014-05-06T16:50:26+00:00
    Version:    b4086720968e0e64af27b4e27d6adbf9fe3854c5
    User:       roanak@isfjeldet.io
    Status:     succeeded
    Slug id:    213526f5-1037-45d7-9bff-cb90ffed2482
    Build output:

    [build output like above]



