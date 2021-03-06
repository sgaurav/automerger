# Automerger
Automatically merge changes from trunk (usually `master`) into feature branches with PRs on GitHub.

## Using

If you don't want to set your server up, [here's the companion app](https://github.com/apps/automerger) hosted by me.

If you want to run your own version, see the [running](#running) section below.


## Why

At [@verloop](https://github.com/verloop), we use feature branches. Whenever a feature would get merged into `master`, we would reach one of two states.

1. All our feature branches would lag behind.

1. Someone would have to hand merge changes from `master` into each feature branch.

If feature branch lagged behind, QA would raise bugs with the title "feature X stopped working on dev/staging".

Needless bugs/issues raised, wasted time, you get the idea.

Hand merging could be automated, so I did that.

For more, scroll to [FAQ](#faq)


## How it works?

Whenever a push is made to the trunk, automerger makes pull requests to all other branches and tries to merge the PRs.

If it fails to do so, it assigns to the pusher of the commit (configurable) for human intervention.

## Running

You can use the executable from [releases](https://github.com/ofpiyush/automerger/releases/latest), or use the `ofpiyush/automerger` [docker image](https://hub.docker.com/r/ofpiyush/automerger).

You can use path `/` or `/automerger` for hosting the app. At the moment the http server makes no assumptions about the path it runs on, but it might add paths like `/stats` or `/metrics` and `/ping` in later versions.

Security comes first, so automerger refuses to run without a webhook secret and verifies signatures of all incoming events.


`--secret` is the only required flag. It must match the secret you provided while setting up the webhook

Automerger can be configured to run in many ways with flags.
### Run with your github app
Use `--integration-id` flag to specify your app's id.
### Run as a user
You can use a token and add a webhook to your repo.  `--token=XXXX`

Set the webhook with the `push` event.
### Track a different branch
Use the `--branch` option to configure the trunk.
### All options
```
Usage of automerger:
  -address string
        Address to listen on. (default "0.0.0.0:3000")
  -api-url string
        URL of github installation. (default "https://api.github.com")
  -assignee value
        Assignees if PR merge fails
        Defaults to the pusher.
  -branch string
        Branch to start PRs from. (default "master")
  -integration-id string
        Github Integration's id. (default "9441")
  -key-file string
        Full path to the Github Integration's private key.
        Either this or token should be present. (default "private_key.pem")
  -merge-method string
        How to merge changes on default branch to current branch. (default "merge")
  -secret string
        Github Integration's secret.
  -token string
        Usable token with access to repo.
        Either this or the key file should be present.
```


## Contributing

Contributions are very welcome and super encouraged.

Two requests:

* If the change is big, open a proposal issue to discuss first.

* Be nice to other people.

## Interesting potential next steps and opportunities to contribute

* Abstract the implementation enough to add support for Bitbucket, GitLab etc.

* Add support for AWS lambda so that hosting this can be cheap/free.

* Add structured logging

* Add metrics endpoint


## FAQ

#### Why do you need to keep feature branches updated?
Because we show our feature branches to the QA.

#### Why don't you use a develop branch and merge features into it first?
Using a develop branch inevitably leads to a cycle `merge to develop` -> `revert to avoid blocking releases` -> `make mistake` -> `land buggy feature in production`

If all changes from master were always merged into feature branches, every branch is `master` + `feature`. It is exactly like the `develop` branch, but without the scope for landing a bad feature in production.

# License

Released under Unlicense. Check the [`LICENSE`](/LICENSE) for more details.

No warranties and public domain for the source code, companion app and hosted version.
