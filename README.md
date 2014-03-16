## CloudFoundry PHP Example Application:  stand alone

This is an example application which can be run on CloudFoundry using the [PHP Build Pack].

This is an example stand alone application.  It is simply a PHP script that runs outside of PHP-FPM as a stand alone process.

### Usage

1. Clone the app (i.e. this repo).

  ```bash
  git clone https://github.com/dmikusa-pivotal/cf-ex-stand-alone
  cd cf-ex-stand-alone
  ```

1. Edit the manifest.yml file.  Change the 'host' attribute to something unique.  Also, notice how the manifest file includes the `no-route: true` attribute.  

1. Push it to CloudFoundry.

  ```bash
  cf push
  ```

  Run `cf logs php-app`.  This should start to tail the logs and you should see the application generate a message every ten seconds.


### How It Works

When you push the application here's what happens.

1. The local bits are pushed to your target.  This is small, two files around 1k.
1. The server downloads the [PHP Build Pack] and runs it.  This installs just PHP.  The reason that no web server is installed is because we set the `WEB_SERVER` option in `.bp-config/options.json` to `none`.
1. Because there is no web server, the build pack knows that the application is a stand alone app and it looks for a main file to execute.  In this case, it finds `app.php` and so it runs it with the command `$HOME/php/bin/php -c "$HOME/php/etc" app.php`.
1. At this point, the build pack is done and CF runs our droplet executing the command from the previous step.


[PHP Build Pack]:https://github.com/dmikusa-pivotal/cf-php-build-pack

