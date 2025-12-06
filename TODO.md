`webconfig` TODOs
=================

- [ ] Move rollout and releases logs to /var/log folders
- [x] Add site as arg to show-releases
- [ ] Keep track of things necesary for server setup
	- "deploy" user
	- "www-data" group
	- nginx install
	- Other deps install?
	- permissions for either deploy user or www-data group
	to:
		- /srv/www
		- /etc/nginx
		- What I did: chgrp -R www-data /etc/nginx; chmod g+w /etc/nginx
	- Add these lines to "sudo visudo":
		- deploy ALL=(root) NOPASSWD: /usr/sbin/nginx -t
		- deploy ALL=(root) NOPASSWD: /usr/bin/systemctl reload nginx
- [ ] Keep track of local setup needs
	- check-deps?