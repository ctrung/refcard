# Configure HTTPS internal port forwarding
Eg. reverse proxy -> jenkins backend : [How to Enable SSL in Jenkins](https://www.youtube.com/watch?v=2uYL4az1BVU)

```shell
firewall-cmd --zone=public --add-service=https                  # https port forwarding step 1
firewall-cmd --add-forward-port=port=443:proto=tcp:toport=8443  # https port forwarding step 2
firewall-cmd --list-forward-ports                               # check port forwarding
firewall-cmd --runtime-to-permanent                             # save permanently
firewall-cmd --reload                                           # reload the runtime config
```
