# Deploy Github repos to Heroku

**To be used with experimental [hk]() Heroku CLI only.**

Find here a few simple scripts that lets you deploy directly from Github to Heroku.

The following is assumed:

* You're using Github as your main collaborative space for code edits
* You've got Heroku set up and you're using the new hk CLI
* You're in a project directory with the 'origin' remote pointing to your canonical Github repo

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
$ git config branch.staging.heroku acme-stagin
```

