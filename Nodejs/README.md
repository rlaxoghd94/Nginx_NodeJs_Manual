# Node Manual

### Install PM2
PM2 is a process manager for Node.js applications. PM2 provides an easy way to manage and demonstrate applications (runs them in the background as a service).

You can easily install PM2 using `npm`, a package manager for Node modules that installs with Node.js. (the `-g` option allows a module to be installed *globally* within the system)
```shell
sudo npm install -g pm2
```

#### Start application
Follow the code below to start the application:
```shell
pm2 start {{Your App File}}.js
```

This will add your application to PM2's process list, which is outputted every time you start an application:

```

[PM2] Spawning PM2 daemon with pm2_home=/Users/NicTaehongKim/.pm2
[PM2] PM2 Successfully daemonized
[PM2] Starting /Users/NicTaehongKim/Desktop/Nic/Computer/Portfolio_worthy/Nginx/Nodejs/hello.js in fork_mode (1 instance)
[PM2] Done.
┌──────────┬────┬─────────┬──────┬───────┬────────┬─────────┬────────┬─────┬───────────┬───────────────┬──────────┐
│ App name │ id │ version │ mode │ pid   │ status │ restart │ uptime │ cpu │ mem       │ user          │ watching │
├──────────┼────┼─────────┼──────┼───────┼────────┼─────────┼────────┼─────┼───────────┼───────────────┼──────────┤
│ hello    │ 0  │ N/A     │ fork │ 21413 │ online │ 0       │ 0s     │ 0%  │ 12.8 MB   │ NicTaehongKim │ disabled │
└──────────┴────┴─────────┴──────┴───────┴────────┴─────────┴────────┴─────┴───────────┴───────────────┴──────────┘
```

As you can see, PM2 automatically assigns an App Name based on the filename and a PM2 **id**. PM2 also manages other information as well, such as the **PID** of the process, its current status, and memory usage.

Applications running under PM2 will be restarted automatically if the application crashes or is killed, but an additional step needs to be taken to get the application to launch on system startup(boot/reboot) via `startup` subcommand.

The `startup` subcommand generates and configures a startup script to launch PM2 and its managed processes on server boots:
```shell
pm2 startup systemd
```
As you can see, when deploying an application *for production-use*, this subcommand will come very handy.

#### Auxiliary PM2 usage

PM2 provides uses with many subcommands to manage or look up information about your application; including `stop`, `restart`, `list` and etc.

To stop an application, you must specify the PM2 `app name` or `id`.
```shell
pm2 stop app_name__or__id
```

To restart an application:
```shell
pm2 restart app_name__or__id
```

To list all the applications currently managed by PM2:
```shell
pm2 list
```

To monitor processes running under PM2 management:
```shell
pm2 monit
```
