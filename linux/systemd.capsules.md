
combine the concept of the per-user service manager and the concept of DynamicUser= into one thing: 

- introduces a new template service: `capsule@.service` similar to `user@.service`

- `user@.service`
  wraps the per-user service manager for regular users
- `capsule@.service`
  can be instantiated many times, and each will spawn a per-user service manager too – but has `DynamicUser=true` set, i.e. it's a service manager for a dynamic, short-lived user.

The idea is that you place some service definition files in the `.config/systemd/user/` subdir of `/var/lib/capsules/<somename>/`. The latter becomes the $HOME of the dynamic user created for capsule@.service.

With that in place, you can then do `systemctl start capsule@<somename>.service` to start the capsule. This spawns a per-user service manager, with a shortlived dynamic user. Once you do `systemctl stop capsule@<somename>.service` it goes away again. And inside that service manager all services defined and enabled in the aforementioned directory will be invoked.

You don't really have to place service files in that dir btw. You can also just start the capsule as-is, and then invoke stuff dynamically inside  it via the `system-run --capsule` switch.
And you can issue commands on and introspect the capsule's service manager via `systemctl --capsule=` and so on.

You can stop a capsule and start it again later if you want, the capsules home directory will survive. Once you are done completely with the capsule's data, use `systemctl clean --all capsule@<somename>.service` to get rid of the home dir and everything else.