`webconfig`
===========
A collection of scripts to help me manage my nginx web server, and the rolling out of new releases of my websites to staging and prod tiers.

The goal of this config is to keep all the logic within a repo I can pull locally, and with the right permissions, be able to manage configuration of my web server, without my server ever needing to have repos on it. The goal is to limit the amount of times I need to log in to my server and do anything, and manage everything with scripts from this repo.

Scripts
-------

**rollout**

Example usage: `./rollout clauslol staging 2025-12-01_00-00-00`

Rolls out `RELEASE_ID: 2025-12-01_00-00-00` to `staging` tier on the website
`clauslol`.

The scripts checks that all args are valid, before attempting to roll it out.

**check-args**
Logic for checking `rollout` arguments, not run directly but rather run from within `rollout`.

**show-releases**
*Work-In-Progress*
TODO: Make it either sort the releases differently, or only display top -N or whatever.

Example usage: `./show-releases clauslol`

Displays all the releases for the given site. Each website repo is responsible for its own deployment of `_site/` builds into a `releases/` directory on the server. So this is just a helper to get the `RELEASE_ID` of the given release to roll out to "staging" or "current".

**show-rollouts**
*Not-yet-developed*

Similar as `show-releases`, except it shows a list of the rollouts over time, as well as the current live configuration.

Other
-----

**site-configs**
Need to figure out how to handle site configuration. I want the nginx configuration files to live in this repo, have some sort of testing mechanism, and then be able to push them to the server.