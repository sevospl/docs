
---
IT IS QUITE PROBABLE THAT SOME INFORMATION HERE IS OUTDATED

[PLEASE OPEN ISSUES](https://github.com/saltyorg/docs/issues)

## Rclone error: Failed to save config file: open /home/\<user\>/.config/rclone/rclone.conf: permission denied

Replace `user` and `group` to match yours' (see [here](FAQ#find-your-user-id-uid-and-group-id-gid)).


```
sudo chown -R user:group ~/.config/rclone/
sudo chmod -R 0755 ~/.config/rclone/
```
