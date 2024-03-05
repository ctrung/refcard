## Commandes

https://jmeter.apache.org/usermanual/get-started.html#load_test_running

En prod, privilégier le mode non GUI : \
```shell
$ export HEAP="-Xms2g -Xmx2g -XX:MaxMetaspaceSize=256m"  # default is 1GB
$ ./jmeter.sh -n -t test.jmx -l test.jtl -e -o folder -Jloop=2
``` 
**-n** : non GUI \
**-t** : test file \
**-l** : log file \
**-e** : generate report dashboard after load test \
**-o** : output folder for report dashboard \
**-j** : parameter (cf. [doc](https://jmeter.apache.org/usermanual/best-practices.html#parameterising_tests))
**-q** : additional properties file

## Run man

https://jmeter.apache.org/usermanual/get-started.html#running

**jmeter.bat**         run JMeter (in GUI mode by default) \
**jmeterw.cmd**        run JMeter without the windows shell console (in GUI mode by default) \
**jmeter-n.cmd**       drop a JMX file on this to run a CLI mode test \
**jmeter-n-r.cmd**     drop a JMX file on this to run a CLI mode test remotely \
**jmeter-t.cmd**       drop a JMX file on this to load it in GUI mode \
**jmeter-server.bat**  start JMeter in server mode \
**mirror-server.cmd**  runs the JMeter Mirror Server in CLI mode \
**shutdown.cmd**       run the Shutdown client to stop a CLI mode instance gracefully \
**stoptest.cmd**       run the Shutdown client to stop a CLI mode instance abruptly

## Test scenarios
### login x1 / run xN / logout x1

![image](https://github.com/ctrung/refcard/assets/68339/34e5b384-7fba-45fe-bcb0-bf38f1163bed)


![image](https://github.com/ctrung/refcard/assets/68339/fb034b8c-b535-425e-83aa-f85928e0277b)


