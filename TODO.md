`webconfig` TODOs
=================

- [ ] Some kind of config file about which site is hosted where? I.e. remove hard-coded "lakelabs" references here and there
	- [ ] Related, maybe this means I need to move "deploy" responsibility out of the individual site repo, and into this repo instead
	- [ ] Add mkdir logic to deploy in case of first build/deployment
- [ ] Make sure on first `rollout` that sites-enabled symlink is set, and nginx is reloaded?
- [ ] Make nice fancy print messages for nginx update scripts just like rollout
- [x] Add site as arg to show-releases
- [ ] Keep track of things necesary for server setup
	- Finish server-setup script!
	- DNS records
	- Other deps install?
- [ ] Keep track of local setup needs
	- check-deps?