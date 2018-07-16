# passport-auth0-loopback

This is the auth0 authentication strategy for Passport.js, modified for compatibility with loopback.

Base on original [passport-auth0](https://github.com/auth0/passport-auth0). Please referring to original documentation for usage, with the following change:

* Pacakge name is changed to `passport-auth0-loopback`.
* `domain` configuration to `auth0Domain`.

## Installation

	npm install passport-auth0-loopback

## Configuration

Take your credentials from the [settings](https://app.auth0.com/#/settings) section in the dashboard and initialize the strategy as follows:

~~~js
var Auth0Strategy = require('passport-auth0-loopback'),
    passport = require('passport');

var strategy = new Auth0Strategy({
   auth0Domain:  'your-domain.auth0.com',
   clientID:     'your-client-id',
   clientSecret: 'your-client-secret',
   callbackURL:  '/callback'
  },
  function(accessToken, refreshToken, extraParams, profile, done) {
    // accessToken is the token to call Auth0 API (not needed in the most cases)
    // extraParams.id_token has the JSON Web Token
    // profile has all the information from the user
    return done(null, profile);
  }
);

passport.use(strategy);
~~~

### State parameter

The Auth0 Passport strategy enforces use of `state` parameter in OAuth 2.0 [authorization requests](https://tools.ietf.org/html/rfc6749#section-4.1.1) and requires session support in Express to be enabled.

If you require the `state` parameter to be omitted (which is not recommended), you can suppress it when calling the Auth0 Passport strategy constructor:

~~~js
var Auth0Strategy = require('passport-auth0-loopback');

var strategy = new Auth0Strategy({
     auth0Domain: 'your-domain.auth0.com',
     // ...
     state: false
  },
  function(accessToken, refreshToken, extraParams, profile, done) {
    // ...
  }
);
~~~

## Usage

~~~js
app.get('/callback',
  passport.authenticate('auth0', { failureRedirect: '/login' }),
  function(req, res) {
    if (!req.user) {
      throw new Error('user null');
    }
    res.redirect("/");
  }
);

app.get('/login',
  passport.authenticate('auth0', {}), function (req, res) {
  res.redirect("/");
});
~~~

This way when you go to ```/login``` you will get redirected to auth0, to a page where you can select the identity provider.

If you want to force an identity provider you can use:

~~~javascript
app.get('/login/google',
  passport.authenticate('auth0', {connection: 'google-oauth2'}), function (req, res) {
  res.redirect("/");
});
~~~

If you want to specify an audience for the returned `access_token` you can:

~~~javascript
app.get('/login',
  passport.authenticate('auth0', {audience: 'urn:my-api'}), function (req, res) {
  res.redirect("/");
});
~~~

If you want to control the OIDC prompt you can use:

~~~javascript
app.get('/login',
  passport.authenticate('auth0', {prompt: 'none'}), function (req, res) {
  res.redirect("/");
});
~~~

