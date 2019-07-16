# Slack Dark Theme
Upgraded version of [slack-black-theme](https://github.com/widget-/slack-black-theme)

# Installation

### Slack 4.0

The code below can be used with Slack version 4.0. For that please follow the next steps:

- Install npx
- Unpack the app.asar with the command `npx asar extract /Applications/Slack.app/Contents/resources/app.asar /Applications/Slack.app/Contents/Resources/app.asar.unpacked`
- Copy the code that you prefer(using external CSS or using local CSS) to the end of the file at `/Applications/Slack.app/Contents/Resources/app.asar.unpacked/dist/ssb-interop.bundle.js`
- Pack the app.asar with the command `npx asar pack /Applications/Slack.app/Contents/resources/app.asar /Applications/Slack.app/Contents/Resources/app.asar.unpacked`

#### Note for older Slack versions:

I haven't tested the CSS for versions before 4.0. I recommend you to update your slack version.

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

  let customCustomCSS = `
  :root {
    /* Modify these to change your theme colors: */
    --primary: #61AFEF;
    --text: #CCC;
    --background: #222;
    --background-elevated: #222;
  }
  `

  // Insert a style tag into the wrapper view
  cssPromise.then(css => {
    let s = document.createElement('style');
    s.type = 'text/css';
    s.innerHTML = css + customCustomCSS;
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
                    s.innerHTML = \`${css + customCustomCSS}\`;
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

  	let customCustomCSS = `
		  :root {
		    /* Modify these to change your theme colors: */
		    --primary: #61AFEF;
		    --text: #CCC;
		    --background: #222;
		    --background-elevated: #222;
		  }
		  `
    if (!err) {
      var css = document.createElement('style')
      css.innerText = data+customCustomCSS;
      document.getElementsByTagName('head')[0].appendChild(css);
    }
  })
});
```

# Custom Colors

You can edit the following lines in order to change most of the theme colors. NOTE: I haven't tested this changes after I've created my own css colors.

```js
/* Modify these to change your theme colors: */
    --primary: #61AFEF;
    --text: #ABB2BF;
    --background: #222;
    --background-elevated: #222;
```
