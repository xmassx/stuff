### Blacking your slack
```
#macos path
cd /Applications/Slack.app/Contents/Resources/app.asar.unpacked/src/static

cat <<EOF >> ssb-interop.js
document.addEventListener('DOMContentLoaded', function() {
 $.ajax({
   url: 'https://raw.githubusercontent.com/xmassx/stuff/master/slack/custom.css',
   success: function(css) {
     let overrides = `
     code { background-color: #535353; color: #85c5ff; } /* Change color: to whatever font color you want */
     .c-mrkdwn__pre, .c-mrkdwn__quote { background: #535353 !important; background-color: #535353 !important; }
     `
     $("<style></style>").appendTo('head').html(css + overrides);
   }
 });
});
EOF
}

#restart slack
# geted from https://dev.to/changoman/easy-dark-mode-for-slack-1mmn
```
