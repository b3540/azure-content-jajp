<properties
	pageTitle="node.js を使用する Azure AD へのサインインおよびサインアウトの概要"
	description="サインインのために Azure AD と連携する node.js Express MVC Web アプリを構築する方法"
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="brandwe"/>

# Azure AD を使用した Web アプリのサインインおよびサインアウト


ここでは、Passport を使用して次のことを行います。

- Azure AD と v2.0 アプリ モデルを使用するアプリにユーザーをサインインします。
- ユーザーについての情報を表示します。
- ユーザーをアプリからサインアウトします。

**Passport** は Node.js 用の認証ミドルウェアです。Passport は、非常に柔軟で高度なモジュール構造をしており、任意の Express ベースまたは Resitify Web アプリケーションに、支障をきたすことなくドロップされます。包括的な認証手法セットにより、ユーザー名とパスワードを使用する認証、Facebook、Twitter などをサポートします。Microsoft Azure Active Directory 用の戦略が開発されています。このモジュールをインストールし、Microsoft Azure Active Directory `passport-azure-ad` プラグインを追加します。

これを行うには、次の手順を実行する必要があります。

1. アプリを登録します。
2. Passport-azure-ad 戦略を使用するようにアプリを設定する
3. Passport を使用して、サインイン要求とサインアウト要求を Azure AD に発行する
4. ユーザーに関するデータを印刷する

このチュートリアルのコードは、[GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS) で管理されています。追加の参考資料として、[アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip)したり、スケルトンを複製したりすることができます:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

The completed application is provided at the end of this tutorial as well.

## 1. Register an App
- Sign into the Azure Management Portal.
- In the left hand nav, click on **Active Directory**.
- Select the tenant where you wish to register the application.
- Click the **Applications** tab, and click add in the bottom drawer.
- Follow the prompts and create a new **Web Application and/or WebAPI**.
    - The **name** of the application will describe your application to end-users
    -	The **Sign-On URL** is the base URL of your app.  The skeleton's default is `http://localhost:3000/auth/openid/return``.
    - The **App ID URI** is a unique identifier for your application.  The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`
- Once you've completed registration, AAD will assign your app a unique client identifier.  You'll need this value in the next sections, so copy it from the Configure tab.

## 2. Add pre-requisities to your directory

From the command-line, change directories to your root folder if not already there and run the following commands:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- In addition, you'll need our `passport-azure-ad` as well:

- `npm install passport-azure-ad`

This will install the libraries that passport-azure-ad depend on.

## 3. Set up your app to use the passport-node-js strategy
Here, we'll configure the Express middleware to use the OpenID Connect authentication protocol.  Passport will be used to issue sign-in and sign-out requests, manage the user's session, and get information about the user, amongst other things.

-	To begin, open the `config.js` file in the root of the project, and enter your app's configuration values in the `exports.creds` section.
    -	The `clientID:` is the **Application Id** assigned to your app in the registration portal.
    -	The `returnURL` is the **Redirect Uri** you entered in the portal.
    - The `clientSecret` is the secret you generated in the portal

- Next open `app.js` file in the root of the proejct and add the follwing call to invoke the `OIDCStrategy` strategy that comes with `passport-azure-ad`


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// logger を追加する

var log = bunyan.createLogger({ name: 'Microsoft OIDC Example Web Application' }); ```

- その後、今参照した戦略を使用してログイン要求を処理します。

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport は、すべての戦略ライターが従うすべての戦略 (Twitter や Facebook など) に対して類似するパターンを使用します。戦略を調べると、それは、パラメーターとして token と done を持つ function() が渡されることがわかります。戦略は、その処理をすべて終えると、必ず戻ってきます。戻ったら、再度要求しなくてもいいように、ユーザーを保存し、トークンを隠します。


> [AZURE.IMPORTANT]上記のコードでは、サーバーに認証を求めたすべてのユーザーを受け入れています。これは、自動登録と呼ばれます。運用サーバーでは、指定された登録プロセスを先に実行していないユーザーにはアクセスを許可しないように設定できます。これは、Facebook への登録は許可するが、その後で追加情報の入力を求めるコンシューマー アプリで通常見られるパターンです。これがサンプル アプリケーションでなければ、返されるトークン オブジェクトから電子メールを抽出した後、追加情報の入力を要求できます。これはテスト サーバーなので、単純にユーザーをメモリ内データベースに追加します。

- 次に、Passport で必要な、ログオンしているユーザーの追跡を可能にするメソッドを追加します。これには、ユーザーの情報のシリアル化と逆シリアル化が含まれます。

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- 次に、express エンジンを読み込むコードを追加します。ここでは、Express が提供する既定の /views と /routes のパターンを使用します。

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- 最後に、実際のログイン要求を `passport-azure-ad` エンジンに渡すルートを追加します。

```JavaScript

// Our Auth routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## 4. Use Passport to issue sign-in and sign-out requests to Azure AD

Your app is now properly configured to communicate with the v2.0 endpoint using the OpenID Connect authentication protocol.  `passport-azure-ad` has taken care of all of the ugly details of crafting authentication messages, validating tokens from Azure AD, and maintaining user session.  All that remains is to give your users a way to sign in, sign out, and gather additional info on the logged in user.

- First, lets add the default, login, account, and logout methods to our `app.js` file:

```JavaScript

// ルート (セクション 4)

app.get('/', function(req, res){ res.render('index', { user: req.user }); });

app.get('/account', ensureAuthenticated, function(req, res){ res.render('account', { user: req.user }); });

app.get('/login', passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }), function(req, res) { log.info('Login was called in the Sample'); res.redirect('/'); });

app.get('/logout', function(req, res){ req.logout(); res.redirect('/'); });

```

-	Let's review these in detail:
    -	The `/` route will redirect to the index.ejs view passing the user in the request (if it exists)
    - The `/account` route will first ***ensure we are authenticated*** (we implement that below) and then pass the user in the request so that we can get additional information about the user.
    - The `/login` route will call our azuread-openidconnect authenticator from `passport-azuread` and if that doesn't succeed will redirect the user back to /login
    - The `/logout` will simply call the logout.ejs (and route) which clears cookies and then return the user back to index.ejs


- For the last part of `app.js`, let's add the EnsureAuthenticated method that is used in `/account` above.

```JavaScript

// ユーザーが認証されることを保証する単純なルート ミドルウェアです。(セクション 4)

// 保護する必要がある任意のリソースでこのルート ミドルウェアを使用します。If // 要求が認証された場合 (通常は永続的なログイン セッションを経由します)、// 要求は先に進みます。それ以外の場合、ユーザーは // ログインページにリダイレクトされます。function ensureAuthenticated(req, res, next) { if (req.isAuthenticated()) { return next(); } res.redirect('/login') } ```

- 最後に、`app.js` でサーバーそのものを実際に作成します。

```JavaScript

app.listen(3000);

```


## 5\.Web サイトにユーザーを表示する express のビューとルートを作成する

`app.js` が完成しました。次に実行する必要があるのは、取得した情報をユーザーに表示するルートとビューを追加し、作成した `/logout` ルートと `/login` ルートを処理することだけです。

- ルート ディレクトリの下に `/routes/index.js` ルートを作成します。

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- ルート ディレクトリの下に `/routes/user.js` ルートを作成します。

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

これらは、要求 (存在する場合はユーザーも) をビューに渡すだけの単純なルートです。

- ルート ディレクトリの下に `/views/index.ejs` ビューを作成します。これは login メソッドと logout メソッドを呼び出して、アカウント情報を取得できるようにする単純なページです。条件付きの `if (!user)` を使用できることに注目してください。要求でユーザーが渡されることは、ログインしているユーザーが存在していることの証拠です。

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>

```

- ルート ディレクトリの下に `/views/account.ejs` ビューを作成し、`passport-azuread` がユーザー要求の中に配置された追加情報を表示できるようにします。

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>

```

- 最後に、レイアウトを追加して、見やすくします。ルート ディレクトリの下に '/views/layout.ejs' ビューを作成します。

```HTML

<!DOCTYPE html>
<html>
	<head>
		<title>Passport-OpenID Example</title>
	</head>
	<body>
		<% if (!user) { %>
			<p>
			<a href="/">Home</a> | 
			<a href="/login">Log In</a>
			</p>
		<% } else { %>
			<p>
			<a href="/">Home</a> | 
			<a href="/account">Account</a> | 
			<a href="/logout">Log Out</a>
			</p>
		<% } %>
		<%- body %>
	</body>
</html>```

Finally, build and run your app! 

Run `node app.js` and navigate to `http://localhost:3000`


Sign in with either a personal Microsoft Account or a work or school account, and notice how the user's identity is reflected in the /account list.  You now have a web app secured using industry standard protocols that can authenticate users with both their personal and work/school accounts.

For reference, the completed sample (without your configuration values) [is provided as a .zip here](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip), or you can clone it from GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


これ以降は、さらに高度なトピックに進むことができます。次のチュートリアルを試してみてください。

[Azure AD を使用することによる Web API の保護](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=September15_HO1-->