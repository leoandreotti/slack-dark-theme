# Slack Dark Theme
Upgraded version of [slack-black-theme](https://github.com/widget-/slack-black-theme)

# Installation

### Slack 4.0

The code below can be used with Slack version 4.0. For that please follow the next steps:

#### For Mac and Linux

- Install npx
- Unpack the app.asar with the command `npx asar extract YOURAPPDIRECTORY/Contents/Resources/app.asar YOURAPPDIRECTORY/Contents/Resources/app.asar.unpacked`
- Copy the code that you prefer(using external CSS or using local CSS) to the end of the file at `YOURAPPDIRECTORY/Contents/Resources/app.asar.unpacked/dist/ssb-interop.bundle.js`
- Pack the app.asar with the command `npx asar pack YOURAPPDIRECTORY/Contents/Resources/app.asar.unpacked YOURAPPDIRECTORY/Contents/Resources/app.asar`

You should use the following app directory:

* Mac: `/Applications/Slack.app/`
* Linux: `/usr/lib/slack/` (Debian-based)

#### For Windows:

* Use 7-Zip with Asar plugin that can be found at [Asar7z](http://www.tc4shell.com/en/7zip/asar/) to extract and pack the app.asar file found at `%homepath%\AppData\Local\slack\app-4.0.0\resources\`
* Copy the code that you prefer(using external CSS or using local CSS) to the end of the file at `%homepath%\AppData\Local\slack\app-4.0.0\resources\app.asar.unpacked\dist\ssb-interop.bundle.js`

** For detailed instructions on the Windows procedure, please check [this post](https://github.com/leoandreotti/slack-dark-theme/issues/16)

#### Important Note:

Please use Help -> Troubleshoot -> Reset Slack to have the theme fully applied. Since it is a main version change, the Clear Cache and Restart option didn't made the magic.

### Slack 3.0

Find your Slack's application directory.

* Windows: `%homepath%\AppData\Local\slack\`
* Mac: `/Applications/Slack.app/Contents/`
* Linux: `/usr/lib/slack/` (Debian-based)

Open up the most recent version (e.g. app-3.3.3) then open resources\app.asar.unpacked\src\static\ssb-interop.js

For versions before 3.0.0, the same code must be added to the following file resources\app.asar.unpacked\src\static\index.js

At the very bottom, add the following code(to use the CSS stored in this repo and receive all my updates):

```js
// First make sure the wrapper app is loaded
document.addEventListener("DOMContentLoaded", function() {

  // Then get its webviews
  let webviews = document.querySelectorAll(".TeamView webview");

  // Fetch our CSS in parallel ahead of time
  const cssPath = 'https://leoandreotti.github.io/slack-dark-theme/slackdarktheme.css';
  let cssPromise = fetch(cssPath).then(response => response.text());

  // Insert a style tag into the wrapper view
  cssPromise.then(css => {
    let s = document.createElement('style');
    s.type = 'text/css';
    s.innerHTML = css;
    document.head.appendChild(s);
  });

  // Wait for each webview to load
  webviews.forEach(webview => {
    webview.addEventListener('ipc-message', message => {
        if (message.channel == 'didFinishLoading')
          // Finally add the CSS into the webview
          cssPromise.then(css => {
              let script = `
                    let s = document.createElement('style');
                    s.type = 'text/css';
                    s.id = 'slack-custom-css';
                    s.innerHTML = \`${css}\`;
                    document.head.appendChild(s);
                    `
              webview.executeJavaScript(script);
          })
    });
  });
});
```
If  you wish to use your own CSS file, clone this repo and use the following code:

```js
document.addEventListener('DOMContentLoaded', function() {

  var fs = require('fs'),
  filePath = '/home/user/slack-dark-theme/docs/slackdarktheme.css';

  fs.readFile(filePath, {encoding: 'utf-8'}, function(err, data) {

  	if (!err) {
      var css = document.createElement('style')
      css.innerText = data;
      document.getElementsByTagName('head')[0].appendChild(css);
    }
  })
});
```

# LICENSE

MIT