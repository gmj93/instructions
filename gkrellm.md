Since GKrellM version 2.3.6 there is an option net_enabled_as_default that will disable new network interfaces from being added automatically.

Close GKRellM

Open the config file with:

```
vim ~/.gkrellm2/user-config
```

Change `net_enabled_as_default` from `1` to `0`

Start GKRellM

After this GKRellM will no longer automatically add all new interfaces it sees.

https://superuser.com/questions/1211387/gkrellm-displays-too-many-virtual-network-interfaces-from-docker 
