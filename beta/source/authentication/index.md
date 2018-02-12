# Authentication
There are two ways to configure authentication to Cluster Manager: 

1. local authentication using the default admin user;    
1. authentication against your Gluu Server(s) using oxd.    

Local authentication is quick and easy. However, for ongoing use, we recommend using Gluu's [oxd OpenID Connect client software](https://gluu.org/docs/oxd) to leverage your Gluu Server for authentication, which will provide SSO. 

## Using default admin user

1. Create `$HOME/.clustermgr/auth.ini`:     
    
    ```
        [user]
        username = admin
        password = secret_password     
    ```
    
1. Login using default user as seen above.

1. Deploy a cluster of Gluu Server.

## Using oxd and Gluu Server

1. Install oxd server:    

    ```    
        apt-get update
    
        apt-get install oxd-server
    ```    
    
1. Configure `oxd-conf.json`:                      
    ```    
    {    
        "server_name":"<OXD_HOSTNAME>",    
        "port":8099,    
        "localhost_only":true,    
        "time_out_in_seconds":0,    
        "use_client_authentication_for_pat":true,    
        "trust_all_certs":true,    
        "trust_store_path":"",    
        "trust_store_password":"",    
        "license_id":"<LICENSE_ID>",    
        "public_key":"<ONELINER_PUBLIC_KEY>",        
        "public_password":"<PUBLIC_PASSWORD>",        
        "license_password":"<LICENSE_PASSWORD>",        
        "support-google-logout":true,    
        "state_expiration_in_minutes":5,    
        "nonce_expiration_in_minutes":5,    
        "public_op_key_cache_expiration_in_minutes":60,    
        "protect_commands_with_access_token":false,    
        "uma2_auto_register_claims_gathering_endpoint_as_redirect_uri_of_client":true,    
        "migration_source_folder_path":"",    
        "storage":"h2",    
        "storage_configuration": {    
        "dbFileLocation":"/opt/oxd-server/bin/oxd_db"    
        }    
    }    
    ```    
    
1. Configure `oxd-default-site-conf.json`:        
    
    ```    
    {    
        "op_host":"",    
        "op_discovery_path":"",    
        "authorization_redirect_uri":"",    
        "post_logout_redirect_uri":"",    
        "redirect_uris":[""],    
        "response_types":["code"],    
        "grant_type":["authorization_code"],    
        "acr_values":[""],    
        "scope":["openid", "profile", "email", "user_name", "permission"],    
        "ui_locales":["en"],    
        "claims_locales":["en"],    
        "client_jwks_uri":"",    
        "contacts":[]    
    }    
    ```    

1. Restart oxd-server:    
    
    ```    
    service oxd-server restart    
    ```    

1. Login to oxTrust using admin privilege. Go to `Users > Manage People` page. Search for `admin` user. When `admin` user found, click the link under UID column.

1. Add `User Permission` attribute. A new form field will appear. Enter `cluster_manager` as its value. Click `Update` button.

1. Create `$HOME/.clustermgr/oxd-client.ini`:        
        
    ```    
    [oxd]    
    host = localhost    
    port = 8099    
    id =     
    
    [client]    
    op_host = https://your.domain.com    
    client_name = ClusterManager    
    authorization_redirect_uri = http://localhost:5000/auth/oxd_login_callback    
    scopes = openid,profile,user_name,permission    
    ```    
        
1. Logout from ClusterManager app.

1. Move `$HOME/.clustermgr/auth.ini` file to another location.

1. Login to ClusterManager app, click `Login with Gluu Server` link. Follow the instructions displayed on your browser.

### Troubleshooting

The first time login using oxd and Gluu Server may returns error message about `user_name` scope being disabled 
in OIDC client configuration. If this error occurs, follow the steps below:
    
1. login to oxTrust    
1. click `OpenID Connect > Clients` submenu        
1. click `ClusterManager` client    
1. click `Add Scope` button at the bottom, and search for `user_name` scope        
1. make sure the `user_name` scope is checked and click `OK` button    
1. click `Update` button at the bottom of the page    
1. open ClusterManager web app and click `Login with Gluu Server`    
    
