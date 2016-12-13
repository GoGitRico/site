---
title: "API Keyroll"
order: 350
---

There are two different concepts here: rolling a **Rack API key** (what Console uses to authenticate with Rack) and rolling your **user** (or **account**) **API key**.


### Roll User/Account API Key

Each Convox account has its own API key (not to be confused with the account password).

| CLI instructions                      | Console instructions                          |
| n/a                                   | "Roll API key" (Account settings page)        |

If you're trying to roll your user API key, you can do that on the [Account page](https://console.convox.com/grid/user/profile).

Click **Roll API Key**. Make sure to store the new API key that will be displayed.

Note that rolling your account API key will disable CLI access until you run `convox login console.convox.com`.


### Roll Rack API key (née "password")

A Rack has a master API key (previously referred to as a "password").

| CLI instructions                      | Convox Console instructions                   | AWS Console instructions                             |
| `convox rack params set Password=`    | "Roll API key" (Rack settings page)           | Change `Password` parameter in CloudFormation stack  |

The “Roll API key” button in Console:

* runs `convox rack params set Password=MySuperDuperNewPassword`
* stores `MySuperDuperNewPassword` in the Rack record in Console
* brokers individual user access via user API keys.

Console stores Rack API keys and acts as a proxy, brokering individual user access to the Rack via User API keys. The Rack API key isn't revealed to you, since you don't need to log into it directly. Instead, you should run `convox login console.convox.com` and then `convox/switch` to activate the Rack.

However, if you ever need to reset this manually (i.e. if console.convox.com is down), you can do so by updating the `Password` parameter in your CloudFormation stack in the AWS console (**Options** -> **Update Stack** -> **Use current template** -> **Password**). Note that resetting the Rack API key in this way will cause Console to lose its connection to the Rack. The Rack will need to be removed and re-added to Console with the new API key.

Note: The Rack will be unavailable during the keyroll. (See FAQ for explanation.)


### Why is the Rack is unavailable during an API key roll?

The Rack will be temporarily unavailable during the keyroll. This is because Console generates a new API key, kicks off a CloudFormation update (to update the Rack's Password param), then stores the new key in DynamoDB. But it takes a while for the CloudFormation update to complete, and until it does, Console is trying to use the new key, whereas the Rack API still has the old key.


## See also

- [API Keys](/docs/api-keys)
- [Login and authentication](/docs/login-and-authentication/)
- [CLI configuration files](/docs/cli-config-files/)
- [CLI environment variables](/docs/cli-environment-variables/)

