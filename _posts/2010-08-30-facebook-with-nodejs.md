---
layout: article
title: Facebook with nodejs
categories: facebook nodejs
updated_at: 2010-08-30
---
Goals
====

  * Create a node/connect/express project called node-fb-chat
  * Get authentication from the user
  * Retrieve a list of friends
  * Subscribe to an update

Mostly ripped from [http://howtonode.org/facebook-connect](http://howtonode.org/facebook-connect)

TODO
---

  * update express
  * Use new fb api
  * Explain methods for facebook.js / jquery.facebook.js

Procedure
====

Overview
----

    node-fb-chat
    |-- app.js /* Your Custom File */
    |-- lib
    |   |-- support
    |   |   |-- express
    |   |   `-- hashlib
    |   |-- facebook.js
    `-- public
        |-- index.html /* Your Custom File */
        |-- xd_receiver.htm
        `-- javascripts
            `-- jquery.facebook.js

Create your project skeleton
----

    PROJECT=~/node-fb-chat # CHANGE THIS to the name of your project
    mkdir -p ${PROJECT}/public/javascripts
    cd ${PROJECT}
    git init

Get `express` for `nodejs`

    cd ${PROJECT}
    mkdir -p lib/support
    git submodule add git://github.com/visionmedia/express.git lib/support/express
    cd lib/support/express
    git submodule init
    git submodule update

Get `hashlib` for `md5` and such in `node`:

    cd ${PROJECT}
    git submodule add git://github.com/brainfucker/hashlib.git lib/support/hashlib
    cd lib/support/hashlib
    make

Get `facebook.js` for `node`

    cd ${PROJECT}
    wget http://github.com/dominiek/node-facebook/raw/master/lib/facebook.js -O \
      lib/facebook.js

Get `jquery.facebook.js` for the browser client

    cd ${PROJECT}
    wget http://github.com/dominiek/node-facebook/raw/master/lib/jquery.facebook.js -O \
      public/javascripts/jquery.facebook.js

Get `xd_receiver.htm` for cross-domain support between facebook and legacy browsers (and legacy facebook)

    cd ${PROJECT}
    wget http://github.com/dominiek/node-facebook/raw/master/examples/fb_iframe/public/xd_receiver.htm -O \
      public/xd_receiver.htm 

`./app.js`:
----

    require.paths.unshift(__dirname + '/../../lib')
    require.paths.unshift(__dirname + '/../../lib/support/express/lib')
    require.paths.unshift(__dirname + '/../../lib/support/hashlib/build/default')

    require('express');
    require('express/plugins');

    configure(function(){
      use(MethodOverride);
      use(ContentLength);
      use(Cookie);
      use(Session);
      use(Logger);
      use(require('facebook').Facebook, {
        apiKey: '261123139091', // Now FACEBOOK_APPLICATION_ID
        apiSecret: 'f91ca59bf13796ecdf20a5e83706db30' // Now FACEBOOK_APPLICATION_SECRET
      });
      set('root', __dirname);
    })

    // Called to get information about the current authenticated user
    get('/fbSession', function(){
      var fbSession = this.fbSession();

      if(fbSession) {
        // Here would be a nice place to lookup userId in the database
        // and supply some additional information for the client to use
      }

      // The client will only assume authentication was OK if userId exists
      this.contentType('json');
      this.halt(200, JSON.stringify(fbSession || {}));
    })

    // Called after a successful FB Connect
    post('/fbSession', function() {
      var fbSession = this.fbSession(); // Will return null if verification was unsuccesful

      if(fbSession) {
        // Now that we have a Facebook Session, we might want to store this new user in the db
        // Also, in this.params there is additional information about the user (name, pic, first_name, etc)
        // Note of warning: unlike the data in fbSession, this additional information has not been verified
        fbSession.first_name = this.params.post['first_name']
      }

      this.contentType('json');
      this.halt(200, JSON.stringify(fbSession || {}));
    })

    // Called on Facebook logout
    post('/fbLogout', function() {
      this.fbLogout();
      this.halt(200, JSON.stringify({}));
    })

    // Static files in ./public
    get('/', function(file){ this.sendfile(__dirname + '/public/index.html') });
    get('/xd_receiver.htm', function(file){ this.sendfile(__dirname + '/public/xd_receiver.htm') });
    get('/javascripts/jquery.facebook.js', function(file){ this.sendfile(__dirname + '/public/javascripts/jquery.facebook.js') });

    run();


`./public/index.html`:
----

Barebones:

    <html>
     <head> 
        <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.3.2/jquery.min.js"></script>
        <script type="text/javascript" src="/javascripts/jquery.facebook.js"></script>
      </head>

      <body>
        <div id="fb-root"></div>
        <script type="text/javascript" src="http://static.ak.connect.facebook.com/js/api_lib/v0.4/FeatureLoader.js.php"></script>

      </body> 
    </html>

With UI:

    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
      "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"> 
    <html xmlns="http://www.w3.org/1999/xhtml"> 
     <head> 
        <script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.3.2/jquery.min.js"></script>
        <script type="text/javascript" src="/javascripts/jquery.facebook.js"></script>
        <script type="text/javascript">
          $(document).ready(function () {
            $.fbInit('FACEBOOK_APPLICATION_ID'); // Formally FACEBOOK_API_KEY
            // EXAMPLE: $.fbInit('261123139091');

            // FB Connect action
            $('#fb-connect').bind('click', function () {
              $.fbConnect({'include': ['first_name', 'last_name', 'name', 'pic']}, function (fbSession) {
                $('.not_authenticated').hide();
                $('.authenticated').show();
                $('#fb-first_name').html(fbSession.first_name);
              });
              return false;
            });

            // FB Logout action
            $('#fb-logout').bind('click', function () {
              $.fbLogout(function () {
                $('.authenticated').hide();
                $('.not_authenticated').show();
              });
              return false;
            });

            // Check whether we're logged in and arrange page accordingly
            $.fbIsAuthenticated(function (fbSession) {
              // Authenticated!
              $('.authenticated').show();
              $('#fb-first_name').html(fbSession.first_name);
            }, function () {
              // Not authenticated
              $('.not_authenticated').show();
            });

          });
        </script>
      </head>

      <body>
        <div id="fb-root"></div>
        <script type="text/javascript" src="http://static.ak.connect.facebook.com/js/api_lib/v0.4/FeatureLoader.js.php"></script>

        <div id="my-account">
          <div class="authenticated" style="display: none">
            Hi there <span id="fb-first_name"></span>!
            <a href="#" id="fb-logout">Logout</a>
          </div>
          <div class="not_authenticated" style="display: none">
            <a href="#" id="fb-connect">Connect with Facebook</a>
          </div>
        </div>
      </body> 
    </html>

Register your application
----

  0. Visit [http://facebook.com/developer](http://facebook.com/developer)
  0. Sign In (to facebook)
  0. [+ Set Up New Application](http://www.facebook.com/developers/createapp.php)
  0. Choose your app name (`node-chat`) for our case (don't use "fb", "facebook", etc in the name)
  0. Do the CAPTCHA
    * If you get "Sorry, an error has occurred.", go back to the [developer page](http://facebook.com/developer) and see if it worked anyway.
    Otherwise you may have three apps.
  0. Select your App and select "Edit Settings"
  0. Web Site > Site URL > http://localhost:7886/
    * CHANGE THIS to suite your situation. This is an example for *my* local development server.

Test your application
----
