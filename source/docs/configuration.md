---
title: Harbor Configuration
description: Explore the Harbor configurations you can use in your workflow. 
extends: _layouts.documentation
section: content
---
# Configuration {#configuration}

### [Introduction](#introduction) {#introduction}
Harbor utilizes environment keys similar to a Laravel application, offering flexibility and the potential for powerful extensions over time.
Using these keys, you can customize the way you provision or destroy a site. Environment keys are defined in the env section of your GitHub action workflow.    

###### [FORGE_TOKEN](#forge-token) (required) {#forge-token}
This key holds your Forge API token, enabling Harbor to communicate with Laravel Forge for site creation and resource management. **Always store this value as an encrypted secret; avoid pasting it directly into your workflow file.**

```yaml
FORGE_TOKEN: ${{ secrets.FORGE_TOKEN }}
```

###### [FORGE_SERVER](#forge-server) (required) {#forge-server}
Specify the server where Harbor should create and deploy a site.  The value to use here is the Forge "Server ID" for the target server.  Examples: 723019, 68342, etc.

```yaml
FORGE_SERVER: 123456
```

###### [FORGE_GIT_REPOSITORY](#forge-git-repository) (required) {#forge-git-repository}
Indicate the Git repository name, such as "mehrancodes/laravel-harbor".

```yaml
FORGE_GIT_REPOSITORY: ${{ github.repository }}
```

###### [FORGE_GIT_BRANCH](#forge-git-branch) (required) {#forge-git-branch}
Provide the Git repository branch name, for instance, "add-new-feature".

```yaml
FORGE_GIT_BRANCH: ${{ github.head_ref }}
```

###### [FORGE_DOMAIN](#forge-domain) (required) {#forge-domain}
Define the website's domain name. Acceptable formats include "laravel-harbor.com" or "api.laravel-harbor.com".

```yaml
FORGE_DOMAIN: laravel-harbor.com
```

[//]: # (###### [FORGE_GIT_PROVIDER]&#40;#forge-git-provider&#41; &#40;required&#41; {#forge-git-provider})

[//]: # (Identify the Git service provider. Options include GitHub, GitLab, etc., with "github" as the default. Refer to the [Forge API documentation]&#40;https://forge.laravel.com/api-documentation#install-new&#41; for more details.)

[//]: # ()
[//]: # (```yaml)

[//]: # (FORGE_DOMAIN: github)

[//]: # (```)

###### [FORGE_SUBDOMAIN_PATTERN](#forge-subdomain-pattern) {#forge-subdomain-pattern}
Harbor constructs the site's subdomain based on your branch name. If your branch name follows a format like **YOUR_TEAM_SHORT_NAME-TICKET_ID** (e.g., **apt-123-added-new-feature**), you can employ a [regex pattern](https://en.wikipedia.org/wiki/Regular_expression) to abbreviate your subdomain name to **apt-123**.

```yaml
FORGE_SUBDOMAIN_PATTERN: /^[a-z]{1,3}-(\d{1,4})/i
```

###### [SUBDOMAIN_NAME](#subdomain-name) {#subdomain-name}
Use this flag to manually set the Forge site subdomain. For example having a value of `pr-${{ github.event.number }}` aims to have a site name of `pr-123.YOUR_DOMAIN.com`.

```yaml
FORGE_DOMAIN: pr-${{ github.event.number }}
```

###### [FORGE_DEPLOY_SCRIPT](#forge-deploy-script) {#forge-deploy-script}
By setting the [Forge Deploy Script](https://forge.laravel.com/docs/sites/deployments.html#deploy-script) environment key, you can seamlessly relay your custom bash deploy script to your new Forge site before project deployment. Here's a basic deploy script example:

```yaml
FORGE_DEPLOY_SCRIPT: "cd $FORGE_SITE_PATH; git pull origin $FORGE_SITE_BRANCH; composer install;"
```

For improved workflow readability and manage your deploy script easier, consider using a [GitHub action variable](https://docs.github.com/en/actions/learn-github-actions/variables).

Consider storing the deploy script below in a variable, e.g., LARAVEL_DEPLOY_SCRIPT:
```bash
cd $FORGE_SITE_PATH
git pull origin $FORGE_SITE_BRANCH
composer install
```

Then reference it inside the workflow like this:
```yaml
FORGE_DEPLOY_SCRIPT: ${{ vars.LARAVEL_DEPLOY_SCRIPT }}
```

###### [FORGE_ENV_KEYS](#forge-env-keys) {#forge-env-keys}
Employ this key to introduce or update your project's custom environment keys, separated by ";":

```bash
FORGE_ENV_KEYS: "GOOGLE_API=${{secrets.GOOGLE_API}}; SMS_API=${{secrets.SMS_API}}"
```

Alternatively, as mentioned in the deploy script section, utilize a secret key for easier management:

```bash
FORGE_ENV_KEYS: ${{ secrets.LARAVEL_ENV_KEYS }}
```

###### [FORGE_NGINX_TEMPLATE](#forge-nginx-template) {#forge-nginx-template}
This key allows you to specify a custom [Nginx configuration](https://forge.laravel.com/docs/servers/nginx-templates.html) template. This is useful when you need to tweak the default Nginx settings to cater to specific requirements of your application.

```yaml
FORGE_NGINX_TEMPLATE: 1234
```

###### [FORGE_NGINX_VARIABLES](#forge-nginx-variables) {#forge-nginx-variables}
When creating a Nginx template, you can use this flag to specify a custom variable.
This is particularly handy when you need to automatically set values inside your Nginx template, such as proxying your Nuxt.js app to a specific port.
You may add as many variables as you like by separating them by a semicolon.

Given you want to add a custom variable to set your Nuxt.js port after you run the application.
In order to proxy the incoming requests from your Nginx template to the given port, you can assign a variable like **{{NUXT_CUSTOM_PORT}}** as a port number on the template.
Now you can add a randomly generated port number to the given flag in your workflow, like below: 

```yaml
FORGE_NGINX_VARIABLES: "NUXT_CUSTOM_PORT:8182"
```

###### [FORGE_PHP_VERSION](#forge-php-version) {#forge-php-version}
Specify the desired PHP version for your application. The default is "php83", but you can set it to other supported versions installed on your server as per your application's requirements.

```yaml
FORGE_PHP_VERSION: php81
```

###### [FORGE_PROJECT_TYPE](#forge-project-type) {#forge-project-type}
Indicate the [type of the project](https://forge.laravel.com/api-documentation#create-site). The default is "php", but depending on your application's stack, you might need to specify a different type.

```yaml
FORGE_PROJECT_TYPE: php
```

###### [FORGE_SITE_ISOLATION](#forge-site-isolation) {#forge-site-isolation}
A flag to determine if [user isolation](https://forge.laravel.com/docs/sites/user-isolation.html) is required. By default, it's set to false.

```yaml
FORGE_SITE_ISOLATION: true
```

###### [FORGE_JOB_SCHEDULER](#forge-job-scheduler) {#forge-job-scheduler}
This flag indicates whether a job scheduler, like Laravel's task scheduler, is needed. By default, it's set to false.

```yaml
FORGE_JOB_SCHEDULER: true
```

###### [FORGE_AUTO_SOURCE_REQUIRED](#forge-auto-source-required) {#forge-auto-source-required}
A flag to determine if environment variables should be auto-sourced during deployment. By default, it's set to false. Enable this if your deployment process requires environment variables to be sourced automatically.

```yaml
FORGE_AUTO_SOURCE_REQUIRED: true
```

###### [FORGE_DB_CREATION_REQUIRED](#forge-db-creation-required) {#forge-db-creation-required}
Indicate if a database should be automatically created during the provisioning process. By default, it's set to false.

```yaml
FORGE_DB_CREATION_REQUIRED: true
```

###### [FORGE_DB_NAME](#forge-db-name) {#forge-db-name}
If present, replaces the username/database name created automatically by formatting the branch name.

```yaml
FORGE_DB_NAME: custom_db_name
```

###### [FORGE_SSL_REQUIRED](#forge-ssl-required) {#forge-ssl-required}
This flag indicates whether SSL certification should be enabled for the site. While the default setting is false, enabling this ensures your site is served securely over HTTPS.

```yaml
FORGE_SSL_REQUIRED: true
```

**Note**: If you enable this, ensure you've added [a wildcard subdomain DNS record](https://en.wikipedia.org/wiki/Wildcard_DNS_record) pointing to your Forge server.


###### [FORGE_QUICK_DEPLOY](#forge-quick-deploy) {#forge-quick-deploy}
This flag allows you to toggle the [Quick Deploy](https://forge.laravel.com/docs/sites/deployments.html#deploy-script) feature. By default, it's set to false.

```yaml
FORGE_QUICK_DEPLOY: true
```

**Caution**: If you intend to enable this feature on your site, ensure the provision workflow does not get triggered when updating your pull request or branch.

###### [FORGE_WAIT_ON_SSL](#forge-wait-on-ssl) {#forge-wait-on-ssl}
A flag to pause the provisioning process until the SSL setup completes. By default, it's set to true, ensuring that the provisioning doesn't proceed until the SSL is fully set up.

```yaml
FORGE_WAIT_ON_SSL: true
```

###### [FORGE_WAIT_ON_DEPLOY](#forge-wait-on-deploy) {#forge-wait-on-deploy}
This flag pauses the provisioning process until the site deployment completes. By default, it's true, ensuring a smooth and complete deployment before any subsequent steps.

```yaml
FORGE_WAIT_ON_DEPLOY: true
```

###### [FORGE_TIMEOUT_SECONDS](#forge-timeout-seconds) {#forge-timeout-seconds}
This flag indicates how much time should be allowed for the deployment process. Defaults to 180 seconds.

```yaml
FORGE_TIMEOUT_SECONDS: 180
```

###### [FORGE_ENVIRONMENT_URL](#forge-environment-url) {#forge-environment-url}
Allows the environment URL that is published as part of the GitHub comment to be overridden with a local environment variable.

```yaml
FORGE_ENVIRONMENT_URL: https://custom-url.test
```
