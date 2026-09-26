


# lib_FranceConnect

# lib_FranceConnect

Convertigo library implementing **FranceConnect v2** (OpenID Connect, authorization code flow) for Convertigo NGX applications.

The OIDC protocol is handled server-side: on the web, the browser is redirected to the FranceConnect login page in the current tab (no popup, so no browser popup permission is needed); on mobile (Cordova), the login page opens in the InAppBrowser. The token exchange, the signature checks and the user info retrieval all run in Convertigo sequences, and the Convertigo session is flagged as authenticated on success.

# Usage

1. Reference `lib_FranceConnect` from your application project.
2. Drop the **FranceConnect** shared component (`Application > NgxApp > FranceConnect`) in one of your pages. It displays the official FranceConnect button and, when the page loads, restores an existing FranceConnect session.
3. Read the connected user from the global `fcConnectedInfo` (see *Globals* below).
4. To log out, call `lib_FranceConnect.Logout`, then send the browser to the returned `logoutUrl` (the demo page `Page` shows how).
5. Override the symbols below for each environment, then register the matching URLs in the FranceConnect partner space.

# Demo application

The library ships with a small **demo application** (`Application > NgxApp`, page `Page`, titled *"Démo utilisation France Connect"*). It is ready to run out of the box and **preconfigured for the "pedro" test environment**:

- It is meant to be deployed on the Convertigo test server **`https://pedro.convertigo.net`**. The redirect URIs, the CORS origin and the application endpoint all point to this server.
- It uses the **FranceConnect v2 sandbox** (`fcp-low.sbx.dev-franceconnect.fr`) with a dedicated sandbox service provider instance. Its test client ID and client secret are the default values of the symbols (demo values, fine to keep in the repository), and the pedro login and logout redirect URLs must be declared for this instance in the FranceConnect partner space.
- Once the project is deployed on pedro, open `https://pedro.convertigo.net/convertigo/projects/lib_FranceConnect/DisplayObjects/mobile/`, click the FranceConnect button and sign in with a sandbox test account. The page then shows *"Welcome <given name> <family name>"*, and the **Logout** buttons end both the Convertigo session and the FranceConnect session.

The demo page is only an example: it shows how to use the **FranceConnect** shared component, the `fcConnectedInfo` global and the `Logout` sequence. Your own applications only need the shared component.

To run the demo, or any application using the library, on another server or against FranceConnect production, override the symbols below. The pedro defaults then no longer apply.

# Symbols

All symbols are declared inline with a default value, so the demo project loads and runs without any configuration. The defaults target the **FranceConnect v2 sandbox** and the **pedro** test server (`pedro.convertigo.net`). Override them in the Administration Console (*Symbols*) for any other environment; the `.secret` symbol is the secured one, its value set in the console is masked.

Symbol names are case-sensitive: the `lib_franceconnect.*` symbols are lowercase, while `lib_FranceConnect.client_secret.secret` and `lib_FranceConnect.corsOrigin` keep the project case.

| Symbol | Used by | Purpose | Default (sandbox) | Production value |
|---|---|---|---|---|
| `lib_franceconnect.clientid` | `getConfiguration` (`OpenIDClientID`, sent to the browser), `loginWithCode` (`client_id` of the token request, `aud` check of the id_token) | Client ID of your FranceConnect service provider instance | `8ad0b8767c411951b730b545661ca6cdc213e2ed41788141862a76a9224dfe4e` (sandbox test instance) | Client ID of your production instance |
| `lib_FranceConnect.client_secret.secret` | `loginWithCode` only (server side, never sent to the browser): `client_secret` of the token request, and the HMAC key when an id_token is signed with `HS256` | Client secret of the same instance. The `.secret` suffix marks it as a **secured symbol**: any value set for it in the Administration Console is masked there | Sandbox demo secret. It is a demo value, so it can live in the repository | **Always override** in the Administration Console (*Symbols*) with the production secret. Never commit a production secret in a default value |
| `lib_franceconnect.scopes` | `getConfiguration` (`Scopes`, sent to the browser and used as the `scope` parameter of the authorize request) | **Space-separated** list of the FranceConnect scopes to request. It must contain `openid`. Each scope adds the matching claims to `fcConnectedInfo` (see *Globals*). Only request the scopes allowed for your service provider (data authorized in your FranceConnect / Datapass file) | `openid family_name given_name` | The scopes your service needs, for example `openid given_name family_name birthdate email` |
| `lib_franceconnect.authorize` | `getConfiguration` (`AuthorizeEndPoint`) | Authorization endpoint opened by the browser | `https://fcp-low.sbx.dev-franceconnect.fr/api/v2/authorize` | `https://oidc.franceconnect.gouv.fr/api/v2/authorize` |
| `lib_franceconnect.tokenendpoint` | `FranceConnect` HTTP connector, *Server* property (HTTPS, port 443) | **Host name only** (no scheme, no path) of the FranceConnect API. Despite its name it serves every server-side call: `api/v2/token`, `api/v2/userinfo` and `api/v2/jwks` | `fcp-low.sbx.dev-franceconnect.fr` | `oidc.franceconnect.gouv.fr` |
| `lib_franceconnect.issuer` | `loginWithCode` (`iss` check of the id_token) | Expected issuer. It must equal the `issuer` published in `<host>/api/v2/.well-known/openid-configuration` | `https://fcp-low.sbx.dev-franceconnect.fr/api/v2` | `https://oidc.franceconnect.gouv.fr/api/v2` |
| `lib_franceconnect.redirecturi` | `getConfiguration` (`RedirectURI`, sent to the browser), `loginWithCode` (`redirect_uri` of the token request) | Login redirect URI: the application URL FranceConnect sends the browser back to with `code` and `state`. It must be the URL of the page hosting the FranceConnect shared component, and it must be declared **exactly** in the partner space | `https://pedro.convertigo.net/convertigo/projects/lib_FranceConnect/DisplayObjects/mobile/` | Your application's URL |
| `lib_franceconnect.endsession` | `Logout` | End-session endpoint used to build `logoutUrl` | `https://fcp-low.sbx.dev-franceconnect.fr/api/v2/session/end` | `https://oidc.franceconnect.gouv.fr/api/v2/session/end` |
| `lib_franceconnect.postlogouturi` | `Logout` (`post_logout_redirect_uri`) | Page FranceConnect redirects to after logout. It must be declared as a logout URL in the partner space | `https://pedro.convertigo.net/convertigo/projects/lib_FranceConnect/DisplayObjects/mobile/` | Your application's URL |
| `lib_FranceConnect.corsOrigin` | Project *CORS origin* property | Origin(s) allowed to call the library's sequences cross-origin (with credentials) | `https://pedro.convertigo.net` | Origin of your application. Not needed when the application is served by the same Convertigo server |

Notes:

- **Sandbox and production are consistent sets.** `clientid`, `client_secret`, `authorize`, `tokenendpoint`, `issuer` and `endsession` must all point to the same FranceConnect environment and the same instance. A mismatch leads to `[FC] émetteur de l'id_token invalide`, `[FC] destinataire de l'id_token invalide` or a token request rejected by FranceConnect.
- **The redirect URIs are not constants.** `redirecturi` and `postlogouturi` must match, character for character, what is declared for the instance in the partner space.
- **CORS:** the Convertigo server's global *CORS policy* can take precedence over the project value. If it is `=Origin`, every origin is accepted whatever `corsOrigin` says, so restrict the global policy on the server too.

# FranceConnect partner space registration

Create one instance per environment on https://espace.partenaires.franceconnect.gouv.fr/ and declare:

- **Login redirect URL**: the URL of the application page hosting the FranceConnect shared component, i.e. the value of `lib_franceconnect.redirecturi`.
  - Sandbox demo (pedro): `https://pedro.convertigo.net/convertigo/projects/lib_FranceConnect/DisplayObjects/mobile/`
  - Your environment: your application's URL, for example `https://<your server>/convertigo/projects/<your project>/DisplayObjects/mobile/`
- **Logout redirect URL**: the page FranceConnect redirects the user to after logout, i.e. the value of `lib_franceconnect.postlogouturi`.
  - Sandbox demo (pedro): `https://pedro.convertigo.net/convertigo/projects/lib_FranceConnect/DisplayObjects/mobile/`
  - Your environment: usually your application's URL
- **Outbound IP address(es)** of the Convertigo server, which calls `api/v2/token`, `api/v2/userinfo` and `api/v2/jwks`
- **Signature algorithm**: `ES256` (recommended) or `RS256`. `HS256` is also supported by the library

Both redirect URLs must match, character for character, the symbol values (`lib_franceconnect.redirecturi` and `lib_franceconnect.postlogouturi`) overridden for the same environment. The web login redirects the current tab to the login page and back to the **login redirect URL**, so it must be a real, served application URL carrying the FranceConnect shared component — not a static relay page: the component itself reads `code` and `state` from the URL on load.

Then copy the instance's client ID and secret into `lib_franceconnect.clientid` and `lib_FranceConnect.client_secret.secret`. For production, set the secret in the Administration Console (*Symbols*): the `.secret` suffix makes its value masked there.

Sandbox test identity providers (for example `https://fip1-low.sbx.fcp.fournisseur-d-identite.fr`) and their test accounts are documented on https://docs.partenaires.franceconnect.gouv.fr/.

# Flow

1. **Button click**: `getConfiguration` returns the client ID, the scopes, the authorize URL and the redirect URI, plus a server-generated `State` and `Nonce`. Each is 43 characters from `SecureRandom` (FranceConnect v2 requires at least 32) and is stored in the HTTP session.
2. The shared action `PerformOIDC` calls `checkAccessOpenID`. If there is no session yet:
   - **Web**: the current tab is redirected to `authorize?client_id&response_type=code&scope=<lib_franceconnect.scopes>&acr_values=eidas1&state&nonce&redirect_uri`. No popup is used, so no browser popup permission is needed.
   - **Cordova**: the same URL opens in the InAppBrowser; when FranceConnect redirects to the redirect URI, the code and state are read from the intercepted URL and exchanged inline.
3. **Web return**: FranceConnect redirects the browser to the application URL (the redirect URI) with `code` and `state`. When the page loads, the FranceConnect shared component reads them, calls `loginWithCode(code, state)`, then cleans the URL.
4. `loginWithCode(code, state)` then:
   - checks `state` against the session value (single use)
   - calls `GetToken` (`POST api/v2/token`)
   - downloads the JWKS (`GetJwks`, `api/v2/jwks`)
   - verifies the id_token: signature (`ES256`/`RS256` with the JWKS key matching `kid`, or `HS256` with the client secret), `iss`, `aud`, `exp` and `nonce` (single use)
   - stores the access token (`oAuthAccessToken`) and the id_token (`fcIdToken`) in the session
   - sets the authenticated user to `fc:<sub>`
   - calls `UserInfo` (`api/v2/userinfo`, a signed JWS), verifies its signature, checks that its `sub` matches the id_token, and returns **all** the verified claims under `fcConnectedInfo`
5. **Logout**: `Logout` builds `logoutUrl = end_session?id_token_hint&state&post_logout_redirect_uri`, clears the session tokens and removes the authenticated user. The client must navigate to `logoutUrl` to end the FranceConnect session.

Any failed check stops the sequence with an explicit error message prefixed by `[FC]`.

# Sequences and transactions

| Object | Accessibility | Authenticated context | Role |
|---|---|---|---|
| `getConfiguration` | Hidden | no | Public OIDC parameters (including `Scopes`) and fresh `State`/`Nonce` |
| `checkAccessOpenID` | Hidden | no | Returns `token=ok` and `fcConnectedInfo` if the session is already connected, otherwise `notoken=true` |
| `loginWithCode` | Hidden | no | Code exchange and all security checks (see *Flow*). Returns `login=ok` and `fcConnectedInfo` |
| `Logout` | Hidden | **yes** | Returns `logoutUrl` and clears the session |
| `FranceConnect.GetToken` / `UserInfo` / `GetJwks` | Private | – | HTTP calls to FranceConnect, only reachable from the sequences |

# Globals

After a successful login, `fcConnectedInfo` (on `router.sharedObject`) holds **all** the claims returned by the FranceConnect userinfo endpoint**, once the signature is verified. The same object is returned by `loginWithCode` and `checkAccessOpenID`, and it is kept in the HTTP session.

The available fields depend on `lib_franceconnect.scopes`:

| Scope | Field(s) in `fcConnectedInfo` |
|---|---|
| `openid` (always) | `sub`: stable FranceConnect subject identifier of the user, specific to your service provider |
| `given_name` | `given_name`: given name(s) |
| `family_name` | `family_name`: birth family name |
| `preferred_username` | `preferred_username`: usage name, if any |
| `birthdate` | `birthdate` (`YYYY-MM-DD`) |
| `gender` | `gender` |
| `birthplace` / `birthcountry` | `birthplace` (INSEE code) / `birthcountry` (INSEE code) |
| `email` | `email` |

See the FranceConnect partner documentation for the complete and current list of scopes. A requested scope that is not allowed for your instance is refused by FranceConnect.

With the default scopes, the demo page displays `fcConnectedInfo.given_name` and `fcConnectedInfo.family_name`.

When no user is connected, `fcConnectedInfo` is **null**. On the server, the authenticated user id is `fc:<sub>`.

# Limitations

- The web login uses a full-page redirect: the redirect URI must be the application URL itself (same page as the FranceConnect shared component), and it must be declared in the partner space.
- Changing `lib_franceconnect.scopes` requires no code change. Claims are returned as sent by FranceConnect: nested claims stay objects, and every value is verified but not transformed.



For more technical informations : [documentation](./project.md)

- [Installation](#installation)
- [Mobile Library](#mobile-library)
    - [Shared Components](#shared-components)
        - [FranceConnect](#franceconnect)


## Installation

1. In your Convertigo Studio click on ![](https://github.com/convertigo/convertigo/blob/develop/eclipse-plugin-studio/icons/studio/project_import.gif?raw=true "Import a project in treeview") to import a project in the treeview
2. In the import wizard

   ![](https://github.com/convertigo/convertigo/blob/develop/eclipse-plugin-studio/tomcat/webapps/convertigo/templates/ftl/project_import_wzd.png?raw=true "Import Project")
   
   paste the text below into the `Project remote URL` field:
   <table>
     <tr><td>Usage</td><td>Click the copy button at the end of the line</td></tr>
     <tr><td>To contribute</td><td>

     ```
     lib_FranceConnect=https://github.com/convertigo/c8oprj-lib-franceconnect.git:branch=master
     ```
     </td></tr>
     <tr><td>To simply use</td><td>

     ```
     lib_FranceConnect=https://github.com/convertigo/c8oprj-lib-franceconnect/archive/master.zip
     ```
     </td></tr>
    </table>
3. Click the `Finish` button. This will automatically import the __lib_FranceConnect__ project


## Mobile Library

Describes the mobile application global properties

### Shared Components

#### FranceConnect

Drop this component in your login page to enable FranceConnect connections



