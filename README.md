## CloudFoundry PHP Example Application:  stand alone & tasks

This is an example application which can be run on CloudFoundry using the [PHP Build Pack].

This is an example stand alone application and example of using CF tasks.  It is simply a PHP script that runs outside of PHP-FPM as a stand alone process.  It also includes a simple task that can be run with `cf run-task`.

### Usage

1. Clone the app (i.e. this repo).

  ```bash
  git clone https://github.com/cloudfoundry-samples/cf-ex-stand-alone.git
  cd cf-ex-stand-alone
  ```

2. Edit the manifest.yml file.  Change the 'host' attribute to something unique.  Also, notice how the [manifest file] includes the `no-route: true` and `health-check-type: process` attributes.  This is important because it instructs CloudFoundry that this application will not handle web requests, which means it will not expect the application to listen on $PORT.

3. Push it to CloudFoundry.

  ```bash
  cf push
  ```

  Run `cf logs php-app`.  This should start to tail the logs and you should see the application generate a message every five seconds.  The application message will look like this.

  ```
  2017-03-14T16:24:09.57-0400 [APP/PROC/WEB/0]OUT 20:24:09 php-app | Current Time From App [08:24:09]
  ```

4. Run the task (optional).

  ```
  cf run-task php-app 'php task.php' --name 'php-task'
  ```

  Run `cf logs php-app`.  This should start to tail the logs and you should see both the application and task generate a message every five seconds.  The task message will look like this.

  ```
  2017-03-14T16:24:11.53-0400 [APP/TASK/3839de9e/0]OUT Current Time From Task [16] [08:24:11]
  ```

  The task should exit after 20 iterations.  You can run `cf tasks` to confirm it exited successfully.

### How It Works

When you push the application here's what happens.

1. The local bits are pushed to your target.  This is small, three files around 1k.
1. The server downloads the [PHP Build Pack] and runs it.  This installs just PHP.  The reason that no web server is installed is because we set the `WEB_SERVER` option in `.bp-config/options.json` to `none`.
1. Because there is no web server, the build pack knows that the application is a stand alone app and it looks for a main file to execute.  In this case, it finds `app.php` and so it runs it with the command `$HOME/php/bin/php -c "$HOME/php/etc" app.php`.
1. At this point, the build pack is done and CF runs our droplet executing the command from the previous step.  It is critical that this application runs forever.  If the application exits, CloudFoundry will think it has crashed and restart it.
1. If you run the task, the task will spin up in a separate container.  CF will execute the command indicated (i.e. `php task.php`) using the same droplet as the application.  The task will run and eventually exit, it should exit 0 to indicate success.  You can run `cf tasks php-app` to confirm it was successful.


[PHP Build Pack]:https://github.com/cloudfoundry/php-buildpack
[manifest file]:https://github.com/cloudfoundry-samples/cf-ex-stand-alone/blob/master/manifest.yml
