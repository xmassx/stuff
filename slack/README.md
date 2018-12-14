### Blacking your slack
```
#macos path
cd /Applications/Slack.app/Contents/Resources/app.asar.unpacked/src/static

cat <<EOF >> ssb-interop.js
document.addEventListener("DOMContentLoaded", function() {

   // Then get its webviews
   let webviews = document.querySelectorAll(".TeamView webview");

   // Fetch our CSS in parallel ahead of time
   // const cssPath = 'https://cdn.rawgit.com/laCour/slack-night-mode/master/css/raw/black.css';
   const cssPath = 'https://raw.githubusercontent.com/xmassx/stuff/master/slack/custom.css';
   let cssPromise = fetch(cssPath).then(response => response.text());

   let customCustomCSS = `
   :root {
      /* Modify these to change your theme colors: */
      --primary: #61AFEF;
      --text: #00b200;
  }
  div.c-message.c-message--light.c-message--hover
  {
    color: #00b200 !important;
  }

  span.c-message__body,
  a.c-message__sender_link,
  span.c-message_attachment__media_trigger.c-message_attachment__media_trigger--caption,
  div.p-message_pane__foreword__description span
  {
      color: #00b200 !important;
      font-family: "Fira Code", Arial, Helvetica, sans-serif;
      text-rendering: optimizeLegibility;
      font-size: 14px;
      letter-spacing: -0.6px !important;
  }
  div.c-virtual_list__scroll_container {
    background-color: #080808 !important;
}
.p-message_pane .c-message_list:not(.c-virtual_list--scrollbar), .p-message_pane .c-message_list.c-virtual_list--scrollbar > .c-scrollbar__hider {
     z-index: 0;
}
div.c-message__content:hover {
    background-color: #080808 !important;
}

div.c-message:hover {
    background-color: #080808 !important;
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

EOF

#restart slack
# geted from https://dev.to/changoman/easy-dark-mode-for-slack-1mmn
```
