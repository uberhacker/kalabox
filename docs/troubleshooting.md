Troubleshooting
===============

Accessing Logs
--------------

Kalabox has a few different log layers to help you diagnose any issues you might be having.

### Install Logs

If you have a failed installation, you should be able to find logs in the following locations...

* **Windows** - `%TEMP%\Setup Log**.txt`
* **macOS** - `/var/log/install.log`
* **Linux** - Differs per system but check common `apt` or `dnf/yum` logs

### Runtime Logs

If you encounter an error during runtime, check out the runtime log at...

  * **macOS/LINUX** - `~/.kalabox/logs/kalabox.log`
  * **Windows** - `C:\Users\{ME}\.kalabox\logs\kalabox.log`

!!! tip "Pro Tip: Use verbose or debug mode!""
    Run the failing command again with either the `-v` or `-d` option to get more useful debug output. But be careful because this output could contain sensitive information.

### Docker Logs

One of the best ways to troubleshoot an issue is to get access to the Kalabox Engine and start hacking around.

!!! tip "Make sure you are ready to run Docker commands on the engine"
    Follow the instructions for [accessing the engine](./general/engine/#accessing-the-engine-directly).

**Some basic Docker commands**

Once you've completed the above you should be able to communicate with your containers. Here are a few helpful commands but please consult the official [Docker documentation](https://docs.docker.com/engine/) for a full spec of commands.

**List all my containers**
`docker ps --all`

**List all core kalabox containers**
`docker ps --all | grep kalabox_`

**List all containers for a particular app**
`docker ps --all | grep myappname`

**Inspect a container**
`docker inspect service_myappname_1`

**Check out the logs for a container**
`docker logs service_appname_1`

**Attach to a container (this is like SSHing)**
`docker exec -i -t service_appname_1 bash`

### Container Logs

While you can get container logs by following some of the steps above you can also access specific container logs by mounting them back out onto your host machine. This is done by modifiying your `kalabox-compose.yml` file, which is a normal [Docker Compose](https://docs.docker.com/compose/compose-file/) with a bunch of extra [environmental variables](users/cli/#env) that Kalabox sets for you.

### Example: Sharing your entire logs directory

Here is a basic example of a `kalabox-compose.yml` `php` service that shares the entire `/var/log` directory of your container to `logs` inside of your app's root directory.

```yml
php:
  image: php-7.0
  hostname: $KALABOX_APP_HOSTNAME
  volumes:
    - $KALABOX_APP_ROOT_BIND/logs:/var/log
```

Common Issues
-------------

### Kalabox is failing to bind to port 80 or 53

In order for Kalabox to work it needs to bind its reverse proxy to port 80 and its dns service to port 53 on your computer. Sadly, these are fairly common ports that are used by a potential myriad of other software such as `apache`, `MAMP`, `Vagrant` etc. Please make sure you've deactivated these services before trying to run Kalabox.

See: [https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=find%20out%20what%20is%20running%20on%20port%2080](https://www.google.com/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=find%20out%20what%20is%20running%20on%20port%2080)

### Behind a network PROXY or FIREWALL

If you are pulling container images and seeing errors like "Error while pulling image: Get https://index.docker.io/v1/repositories/kalabox/proxy/images: x509: certificate signed by unknown authority" in your macOS/Linux installer log then you might be behind a network proxy or firewall that is preventing you from pulling the needed Kalabox dependencies.

Check out [https://github.com/kalabox/kalabox/issues/1635](https://github.com/kalabox/kalabox/issues/1635) for more details on that issue.

### Windows is also running VirtualBox

In some cases you cannot use VirtualBox (a common development tool) with Hyper-V however there is a documentated workaround you can check out over at [https://derekgusoff.wordpress.com/2012/09/05/run-hyper-v-and-virtualbox-on-the-same-machine/](https://derekgusoff.wordpress.com/2012/09/05/run-hyper-v-and-virtualbox-on-the-same-machine/)

The author says, "VirtualBox and Hyper-V cannot co-exist on the same machine. Only one hypervisor can run at a time, and since Hyper-V runs all the time, while VirtualBox only runs when it is launched, VirtualBox is the loser in this scenario."
