# mixcloudWrapper

An unofficial [Mixcloud](https://www.mixcloud.com/) wrapper created in Electron.

## Core Features

 - A smart cross-platform logger
 - A ready-to-go auto updater
 - Built-in support for [Electron Builder][electron-builder]
 - Built-in support for different _build targets_ (ie. release, debug, test, etc) with different settings
 - A printer management feature (optional, fully supported on 64bit Mac/Linux)

### Targeted builds

Sometimes you need to build different versions of your application, for example for releasing it to the public, for testing, for QA, etc. And each of these targets could require different settings (i.e. URL to load, debug enabled/disabled).

Each target is a JSON file stored in `build/targets`, so you can have as many targets you need. The content of the selected file is merged with the `config` object loaded from `app/package.json`, allowing you to override default settings such as debug or update URL.

The default build target is "release", which builds a production version of the application with developer and debug tools disabled. You can build other targets by setting the `BUILD_TARGET` env var.

~~~ console
$ BUILD_TARGET=debug npm run build:osx
~~~

### Global settings

Once config is loaded from `app/package.json`, the `global.appSettings` variable is available to all backend modules, in frontend modules you can use `require('remote').getGlobal('appSettings').someProperty`.


## Build/Update Workflow

A suitable build flow can be:

 - Version bump in `app/package.json`
 - Run `build` command for specific targets (dev/test/qa/release) platforms
 - Publish the packages
   * create the remote release(s) for target and platform
   * upload the assets

### Build Notes

Although theoretically you can cross-build all platform versions of your app on a single operating system, I've found that building each release on the target OS, using VMs obviously, works best.


For Windows builds, use an official [Windows 10 development VM][win10dev] from Microsoft. You just need to install Python, Node and Git.

**Note**: the `author.name` field from `app/package.json` is used by Squirrel as a path component for the shortcut menu icon, so double-check it for any special characters not allowed in Windows paths.

The Linux build may fail if GraphicsMagick does not find suitable sizes inside the app Icon. Required sizes are: 16x16x32, 32x32x32, 256x256x32, 512x512x32, 1024x1024x32.

For more detailed and updated information, you can check the official build instructions for [Linux][buildlinux], [OS X][buildmac] and [Windows][buildwin].

## How To Sign The Installers

In order to sign the installers you need to obtain 2 code-signing certificate in `*.p12` format: one for Windows and one for OSX. The certificate must be accessible from the building machine via HTTPS (ie. GDrive, Dropbox).

Then on Windows PowerShell:

~~~ console
PS C:\src\skelektron> $env:CSC_LINK="https://url.to.my/certificate.p12"
PS C:\src\skelektron> $env:CSC_KEY_PASSWORD="CertificatePassword"
PS C:\src\skelektron> npm run build:win -- --sign
~~~

And from OSX Terminal:

~~~ console
$ export CSC_LINK="https://url.to.my/certificate.p12"
$ export CSC_KEY_PASSWORD="CertificatePassword"
$ npm run build:osx
~~~

Mac certificates are provided by Apple through XCode. To export a certificate in `p12` format [read this Apple doc][applecertdoc].

## Running the Tests

`npm test` will run both unit and integration tests (Spectron). To run each test separately use `npm run unit` and `npm run integration`.

## Contributing

See CONTRIBUTING file.

## Credits

The [Electron](http://electron.atom.io) and [Electron Builder][electron-builder] team. [Arek Sredzki](http://arek.io/) for [Electron Release Server][ers], Ion for [printer][printer]. [Marco Piraccini](https://github.com/marcopiraccini) and [Bogdan Rotund](https://github.com/Bogdan-Rotund). [Kilian Valkhof][kilian-valkhof] for these UI [pro tips][kilian-tips].

## Contributor Code of Conduct

Please note that this project is released with a [Contributor Code of
Conduct](http://contributor-covenant.org/). By participating in this project
you agree to abide by its terms. See CODE_OF_CONDUCT file.

## License

mixcloudWrapper is released under the MIT License. See the bundled LICENSE file for details.

[ers]: https://github.com/ArekSredzki/electron-release-server
[printer]: https://www.npmjs.com/package/printer
[printer-examples]: https://github.com/tojocky/node-printer/tree/master/examples
[electron-builder]: https://www.npmjs.com/package/electron-builder
[electron-builder-wiki]: https://github.com/electron-userland/electron-builder/wiki
[devtron]: http://electron.atom.io/devtron/
[spectron]: http://electron.atom.io/spectron/
[istanbul]: https://www.npmjs.com/package/istanbul
[mocha]: https://www.npmjs.com/package/mocha
[chai]: https://www.npmjs.com/package/chai
[electron-quick-start]: https://github.com/electron/electron-quick-start
[electron-api-demos]: https://github.com/electron/electron-api-demos
[win10dev]: https://developer.microsoft.com/en-us/windows/downloads/virtual-machines
[win7dev]: https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/windows/
[msvcpp]: https://www.microsoft.com/en-us/download/details.aspx?id=5555
[vs2015]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[buildlinux]: http://electron.atom.io/docs/development/build-instructions-linux/
[buildmac]: http://electron.atom.io/docs/development/build-instructions-osx/
[buildwin]: http://electron.atom.io/docs/development/build-instructions-windows/
[builderoptions]: https://github.com/electron-userland/electron-builder/wiki/Options
[applecertdoc]: https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html#//apple_ref/doc/uid/TP40012582-CH31-SW7
[fpm]: https://github.com/jordansissel/fpm
[ers-s3]: https://github.com/ArekSredzki/electron-release-server/issues/15
[sails-session]: http://sailsjs.org/documentation/reference/configuration/sails-config-session
[jawsdb]: https://elements.heroku.com/addons/jawsdb
[heroku-redis]: https://elements.heroku.com/addons/heroku-redis
[kilian-valkhof]: https://blog.avocode.com/authors/kilian-valkhof
[kilian-tips]: https://blog.avocode.com/blog/4-must-know-tips-for-building-cross-platform-electron-apps
