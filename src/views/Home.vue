<template>
  <div class="home">
    <div id="okta-login-container"></div>
  </div>
</template>

<script>
// @ is an alias to /src
import OktaSignIn from '@okta/okta-signin-widget';
import '@okta/okta-signin-widget/dist/css/okta-sign-in.min.css';
export default {
  name: 'Home',
  components: {
    
  },
  mounted() {
  //     var signIn = new OktaSignIn({baseUrl: 'https://dev-713558.okta.com'});
  // signIn.renderEl({
  //   el: '#widget-container'
  // }, function success(res) {
  //   if (res.status === 'SUCCESS') {
  //     console.log('Do something with this sessionToken', res.session.token);
  //   } else {
  //   // The user can be in another authentication state that requires further action.
  //   // For more information about these states, see:
  //   //   https://github.com/okta/okta-signin-widget#rendereloptions-success-error
  //   }
  // });
var oktaSignIn = new OktaSignIn({
    baseUrl: 'https://dev-713558.okta.com',
    clientId: '0oa271kw2XXQklR6w4x6',
    authParams: {
      issuer: 'default',
      responseType: ['token', 'id_token'],
      display: 'page'
    }
  });
  if (oktaSignIn.hasTokensInUrl()) {
    oktaSignIn.authClient.token.parseFromUrl().then(function success(tokens) {
        // Save the tokens for later use, e.g. if the page gets refreshed:
        // Add the token to tokenManager to automatically renew the token when needed
        tokens.forEach(token => {
          if (token.idToken) {
            oktaSignIn.authClient.tokenManager.add('idToken', token);
          }
          if (token.accessToken) {
            oktaSignIn.authClient.tokenManager.add('accessToken', token);
          }
        });

        // Say hello to the person who just signed in:
        oktaSignIn.authClient.tokenManager.get('idToken').then(function (idToken) {
          console.log('Hello, ' + idToken.claims.email);
        });

        // Remove the tokens from the window location hash
        window.location.hash = '';
      },
      function error(err) {
        // handle errors as needed
        console.error(err);
      }
    );
  } else {
    oktaSignIn.authClient.session.get().then(function (res) {
      // Session exists, show logged in state.
      if (res.status === 'ACTIVE') {
        console.log('Welcome back, ' + res.login);
        return;
      }
      // No session, show the login form
      oktaSignIn.renderEl(
        {el: '#okta-login-container'},
        function success(res) {
          console.log(res);
          // Nothing to do in this case, the widget will automatically redirect
          // the user to Okta for authentication, then back to this page if successful
        },
        function error(err) {
          // handle errors as needed
          console.error(err);
        }
      );
    });
  }
  }

}
</script>
