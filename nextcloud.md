# Random Configs about nextcloud

## Upgrade SSD storage
* First make a backup of the files in the original SSD.
* Find the UUID of the SSD in the "Disks" app. Or use "sudo blkid" in terminal
* Create `/etc/udev/rules.d/80-local.rules` file and append the following line to it
  ```
  ACTION=="add", KERNEL=="sd?", ENV{ID_FS_UUID}=="<UUID>", SYMLINK+="<some name to come up in /dev>"
  ```
  This line adds a symbolic `/dev/some name` to be used by docker to be able to access it inside a docker container
  using `devices` in the docker-compose file.
* run `sudo udevadm control --reload-rules && sudo udevadm trigger` to update the rules
* Now modify the `/etc/fstab` file to fix the mount point of the SSD on startup. Add the following line to the file
  ```
  UUID=<UUID> <Absolute path mount point> auto quiet,defaults,permissions,umask=007,uid=33,gid=33 0 2
  ```
  change `<UUID>` and `<Absolute path mount point>` to what you like.
* `sudo mount -a` re-reads the `fstab` file and mount SSDs according to that file.
* Now change the directories and links according in the docker-compose file.
