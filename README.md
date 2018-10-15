# Slack Dark Theme
Upgraded version of [slack-black-theme](https://github.com/widget-/slack-black-theme)

# Instalation

Find your Slack's application directory.

* Windows: `%homepath%\AppData\Local\slack\`
* Mac: `/Applications/Slack.app/Contents/`
* Linux: `/usr/lib/slack/` (Debian-based)

Open up the most recent version (e.g. app-3.3.3) then open resources\app.asar.unpacked\src\static\ssb-interop.js

For versions before 3.0.0 the same code must be added to the following file resources\app.asar.unpacked\src\static\index.js

At the very bottom, add the following code:

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
    --text: #ABB2BF;
    --background: #282C34;
    --background-elevated: #3B4048;
  }

  div.c-message.c-message--light.c-message--hover,
  .p-flexpane_header,
  .p-shortcuts_flexpane__title,
  .p-history_container,
  .c-message_list__day_divider,
  .c-message_list__day_divider__label,
  .c-message_list__day_divider__label__pill,
  .p-message_pane .c-message_list.c-virtual_list--scrollbar>.c-scrollbar__hider:before,
  .p-message_pane .c-message_list:not(.c-virtual_list--scrollbar):before,
  .c-keyboard_key {
    color: #fff !important;
    background-color: #222 !important;
  }

  .unread_group_header {
    background-color: #222 !important;
  }

 
  .p-message_pane .p-message_pane__top_banners:not(:empty)+div .c-message_list.c-virtual_list--scrollbar>.c-scrollbar__hider:before,.p-message_pane .p-message_pane__top_banners:not(:empty)+div .c-message_list:not(.c-virtual_list--scrollbar):before{
      box-shadow:0 32px #222;
  }

  .p-message_pane .c-message_list.c-virtual_list--scrollbar>.c-scrollbar__hider,.p-message_pane .c-message_list:not(.c-virtual_list--scrollbar){
      background:#222;
  }

  span.c-message__body,
  a.c-message__sender_link,
  span.c-message_attachment__media_trigger.c-message_attachment__media_trigger--caption,
  div.p-message_pane__foreword__description span,
  .c-message_attachment,
  .p-shortcuts_flexpane__shortcut_title {
    color: #afafaf !important;
  }

  pre.special_formatting {
    background-color: #222 !important;
    color: #8f8f8f !important;
    border: solid;
    border-width: 1 px !important;
  }

  .c-message,
  .c-virtual_list__item,
  .c-member_slug--link {
    background-color: #222 !important;
  }

  .c-menu {
    background-color: #fff !important;
  }

  #client_body:not(.onboarding):not(.feature_global_nav_layout):before {
  background-color: #222 !important;
  border: 1px solid #222 !important;
  box-shadow: none !important;
  }

  .ql-placeholder, .c-team__display-name, .c-unified_member__display-name, .c-usergroup__handle, .timezone_value, .timezone, .p-message_pane__foreword__description, .charcoal_grey, .sidebar_menu_list_item {
    color: #ccc !important;
  }

  div[role=listitem]:first-child {
    margin-bottom: 20px !important;
  }

  div[role="presentation"] { background-color: #282C34 !important; }

  .c-menu_item__li {
    background-color: #FFF !important;
  }

  .c-search__input_and_close{
      background:#292d32!important;
      color white !important;
  }
  .c-search__input_box{
      background:#292d32!important;
      color white !important;
  }
  .c-search__input_and_close{
      background:#292d32!important;
      color white !important;
  }
  .c-search_autocomplete{
      background:#b7bcbe!important;
      color white !important;
  }
  .c-search_autocomplete footer{
      background:#97a0a5!important;
      color white !important;
  }
  .c-search_autocomplete__suggestion_text{
      color white !important;
  }
  .c-search_autocomplete__suggestion_content .c-search_autocomplete__suggestion_text{
      color white !important;
  }
  .c-search_autocomplete header{
      background:#b7bcbe!important;
      color:black !important;
  }
  .c-search_autocomplete footer .c-search_autocomplete__footer_navigation_help{
      color:white !important;
  }
  .c-search__input_box .c-search__input_box__input .ql-editor, .c-search__input_box .c-search__input_box__input .ql-placeholder{
      background:#2b2c2e!important;
      border:none !important;
  }
  .c-search__tabs{
      background:#2b2c2e!important;
  }
  .c-search__view{
      background:#2b2c2e!important;
  }
  .c-search_message__body{
      color:#cacbcc !important;
  }
  .p-search_filter__title_text{
      background:#2b2c2e!important;
      color:white !important;
  }
  .p-search_filter__title:before{
      color:grey !important;
  }
  .p-search_filter__dates{
      background:#1f2021!important;
      border: none !important;
      color:#cacbcc !important;
  }
  .p-search_filter__datepicker_trigger:hover{
      color:white !important;
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
# Custom Colors

You can edit the following lines in order to change most of the theme colors. NOTE: I haven't tested this changes after I've created my own css colors.

```js
/* Modify these to change your theme colors: */
    --primary: #61AFEF;
    --text: #ABB2BF;
    --background: #282C34;
    --background-elevated: #3B4048;
```