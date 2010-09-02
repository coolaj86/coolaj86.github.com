---
layout: article
title: facebook with node.js
categories: facebook nodejs
updated_at: 2010-09-02
---
Goal
====

Use Facebook's JavaScript SDK to establish a connection client-side and pass that connection to Node.js.

This is effectively a node port of [Single Sign-on with the JavaScript SDK](http://developers.facebook.com/docs/authentication/).

Connect Skeleton
====

`app.js`:

    var connect = require('connect'),
      fb_sso_app = require('fb_single_sign_on'),
      host  = connect.vhost('localhost', connect.createServer(function(req, res){
        res.writeHead(200, {});
        res.end('test localhost (vhost)');
      }));

    var fb_sso = connect.vhost('fb.localhost', connect.createServer(fbsso_app));

    connect.createServer(
        // Shared middleware
        connect.logger(),
        connect.cookieDecoder(),
        host,   // http://localhost:7886 server with own middleware
        fb_sso   // http://fb.localhost:7886 server with own middleware
    ).listen(7886);

Facebook Auth via Cookie
====

`fb_single_sign_on.js`:

    var FACEBOOK_APP_ID = '123456789012',
      FACEBOOK_SECRET = '0123456789abcdef0123456789abcdef',
      querystring = require('querystring'),
      crypto = require('crypto'),
      http = require('http');

    /* Parses FB Sub-cookie */
    function get_facebook_cookie(app_id, application_secret, cookies) {
      var args = [],
        payload = '',
        fb_cookie;

      fb_cookie = cookies['fbs_' + app_id] || '';
      args = querystring.parse(fb_cookie.replace(/\\/,'').replace(/"/,''));

      var keys = Object.keys(args).sort();
      keys.forEach(function(key) {
        var value = args[key];
        if (key !== 'sig') {
          payload += key + '=' + value;
        }
      });

      var digest = crypto.createHash('md5').update(payload + application_secret).digest('hex');

      if (digest !== args.sig) {
        return null;
      }
      return args;
    }

    /* Connect- / Expess-able application  */
    function single_sign_on_example(req, resp) {
      var cookie = get_facebook_cookie(FACEBOOK_APP_ID, FACEBOOK_SECRET, req.cookies);
      console.log('Try `curl https://graph.facebook.com/me?access_token=' + cookie.access_token + '` - it\'s fun!');

      var present = cookie ? "Your user ID is " + cookie.uid : "<fb:login-button></fb:login-button>";
      var body = '<!DOCTYPE html>\n' +
      '<html xmlns="http://www.w3.org/1999/xhtml"\n' +
      '      xmlns:fb="http://www.facebook.com/2008/fbml">\n' +
      '  <body>\n' +
      present + '\n' +
      '    <div id="fb-root"></div>\n' +
      '    <script src="http://connect.facebook.net/en_US/all.js"></script>\n' +
      '    <script>\n' +
      '      FB.init({appId: ' + FACEBOOK_APP_ID + ', status: true,\n' +
      '               cookie: true, xfbml: true});\n' +
      '      FB.Event.subscribe("auth.login", function(response) {\n' +
      '        window.location.reload();\n' +
      '      });\n' +
      '    </script>\n';
      resp.writeHead(200);
      resp.write(blah);

      var fb_client = http.createClient('443', 'graph.facebook.com', true);
      console.log('created client');

      var graph_request = fb_client.request('GET', '/me?access_token=' + cookie.access_token,
        {'host': 'graph.facebook.com'});

      console.log('created request');
      graph_request.end();

      graph_request.on('response', function (response) {
        console.log('STATUS: ' + response.statusCode);
        console.log('HEADERS: ' + JSON.stringify(response.headers));
        response.setEncoding('utf8');

        response.on('data', function (chunk) {
          console.log('BODY: ' + chunk);
          resp.write(chunk);
        });

        response.on('end', function () {
          console.log('END');
          resp.end('  </body>\n</html>\n');
        });

      });
    }

    module.exports = single_sign_on_example;
