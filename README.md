[![Quality Gate Status](https://sonarcloud.io/api/project_badges/measure?project=molgenis_molgenis-frontend&metric=alert_status)](https://sonarcloud.io/dashboard?id=molgenis_molgenis-frontend)
[![Code Coverage](https://codecov.io/gh/molgenis/molgenis-frontend/branch/master/graph/badge.svg)](https://codecov.io/gh/molgenis/molgenis-frontend/branch/master)
[![Commitizen friendly](https://img.shields.io/badge/commitizen-friendly-brightgreen.svg)](http://commitizen.github.io/cz-cli/)
# molgenis-frontend
These are *stable* apps for MOLGENIS. The *stable* apps will compose the MOLGENIS frontend. These are used in the [molgenis/molgenis repository](https://github.com/molgenis/molgenis)

The following will be addressed:

- [Environment](#environment)
- [Commits](#commits)
- [Developing](#developing)
- [Integrate with MOLGENIS](#integrate-with-molgenis)
- [Guidelines](#guidelines)

## Environment
When developing client code, you will need to following tools:
 - Node v8.9.0 (LTS version) and included NPM version
 - Yarn v1.10.3 or greater

The project is built using [lerna](https://lernajs.io/) to manage the monorepo and [yarn](https://yarnpkg.com/) as the npm client.

## Developing
To get started run
```bash
> yarn install
> yarn lerna bootstrap
```

The following will be addressed:

- [Commits](#commits)
- [Usage of yarn with Vue](#usage-of-yarn-with-vue)
- [Update existing stable apps](#update-existing-stable-apps)
- [Create new stable apps](#create-new-stable-apps)

### Commits
We use independent versioning for the apps. This makes it hard to interactively specify at release
time what type of upgrade each app needs.

So please specify your changes using [conventional commits](https://www.conventionalcommits.org).
This allows lerna to deduce the version increments for each app from the commits.
As a bonus, it will generate a per-app changelogs.

If you've run `yarn install`, you can use [`commitizen`](https://commitizen.github.io/cz-cli/) to
formulate your commits interactively from anywhere in the repository. Just type `git cz` instead
of `git commit`. It will accept command line flags too.

#### Allowed scopes
Use the app directory names as scopes.

#### Allowed types
The [Angular conventional commit types](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#type).

#### Squash messy commits
When merging a pull request that does not use conventional commits you can squash the commits
into a single conventional commit.

### Usage of yarn with Vue
The following three commands can be used to install, develop and test client code:

```bash
> yarn install
> yarn serve
> yarn test
```

Third party dependencies can be added: 

```bash
> yarn add <library_name>
```

Third party development dependencies can be added: 

```bash
> yarn add --dev <library_name>
```

or removed:

```bash
> yarn remove <library_name>
```

using yarn.

Yarn produces a `yarn.lock` file. 
Commit this file to your Git repository as it ensures future builds to use the versions that were used to create the client code.

When running your client code in development on port 8081, it will help to run the MOLGENIS locally on port 8080. 
The vue-cli comes with a proxy table that will redirect any REST calls to localhost:8080.

### Update existing stable apps
**You have to update the [package.json](https://github.com/molgenis/molgenis/blob/master/molgenis-frontend/package.json) in the [molgenis/molgenis](https://github.com/molgenis/molgenis) repository before you start developing in the frontend stable apps. The version of the app you start to develop has to be updated to [ *canary* ]**. 

That way each merge with the master on this repository will be automatically picked up by the main MOLGENIS repository.

The whole development flow is working as follows. 

- [PR's](#pull-requests)
- [Merges with master](#merges-with-master)
- [Releases](#Releases)

#### Pull request
When you start developing and define a set of changes to be a pull-request it is not published on the [stable appstore](https://registry.molgenis.org/#browse/browse:npm-stable) by default. 
The changes have to be tested by hand. You test the changes by proxying the app to MOLGENIS and see if it works.

#### Merges with master
When you merge a pull-request it produces a fixed number (an alpha release) and a 'canary-tag' on the [stable appstore](https://registry.molgenis.org/#browse/browse:npm-stable).
By setting the canary-tag in the MOLGENIS repository you automatically pickup new changes from the MOLGENIS frontend when you build the MOLGENIS repository.

#### Releases
You can manually perform a release on Jenkins.
Lerna will determine which modules need to be released and what type of increment is needed based
on the git log. Please use conventional commits so that this is determined correctly.

For more information, see the `lerna version` [command documentation](https://github.com/lerna/lerna/tree/master/commands/version#readme).

The modules are published to our [stable appstore](https://registry.molgenis.org/#browse/browse:npm-stable) 
`https://registry.molgenis.org/repository/npm-stable/`

### Create new stable apps
If you do not have the vue-cli (version 3), please check [vue-cli](https://cli.vuejs.org/). 

If you have the vue-cli installed you can use the following steps to quickly install a working Vue template.

```bash
> cd packages/
> vue create --preset ../preset.json <new-app-name> 
```

Using the ```preset.json``` is recommended. If you need specific tooling you can also choose manual.

>note: Make sure you update the project-name to @molgenis/#new-app# for publishing purposes

## Integrate with MOLGENIS
There is a workflow you have to follow to implement new frontend code in [molgenis/molgenis-frontend](https://github.com/molgenis/molgenis/blob/master/molgenis-frontend/).

### Add to [package.json](https://github.com/molgenis/molgenis/blob/master/molgenis-frontend/package.json)
Make sure you add the new app to the [package.json](https://github.com/molgenis/molgenis/blob/master/molgenis-frontend/package.json).

```bash
> cd molgenis/molgenis-frontend
> 
> yarn add <new-app>
```

Change the version into **canary**.

### Add to [pom.xml](https://github.com/molgenis/molgenis/blob/master/molgenis-frontend/pom.xml) as well.
You have to include the package in the [pom.xml](https://github.com/molgenis/molgenis/blob/master/molgenis-frontend/pom.xml) as well.

```
...
<build>
  <resources>
    <resource>
      <directory>node_modules/@molgenis/##new-app##/dist</directory>
    </resource>
...
```

### Create a [molgenis/molgenis-new-app](https://github.com/molgenis/molgenis) module to serve the new app
You need three files:

- pom.xml
- src/main/java/org/molgenis/new/app/controller.java
- src/main/resource/templates/view-new-app.ftl

You have to create a module with the following inside your ```pom.xml```:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <parent>
    <groupId>org.molgenis</groupId>
    <artifactId>molgenis</artifactId>
    <version>#molgenis version#</version>
  </parent>

  <artifactId>molgenis-new-app</artifactId>
  <name>#new app#</name>
  <description>Plugin module for serving new app.</description>

  <!-- start optional -->
  <dependencies>
    <dependency>
      <groupId>org.molgenis</groupId>
      <artifactId>#depedant module#</artifactId>
      <version>${project.version}</version>
    </dependency>
  </dependencies>
  <!-- end optional -->  
</project>
```

You create in ```src/main/resources/templates/``` a ```view-new-app.ftl``` file with the content below.

```injectedfreemarker
<#include "resource-macros.ftl">
<#include "molgenis-header.ftl">
<#include "molgenis-footer.ftl">

<#assign js = []>
<#assign css = ["#new app#/app.css"]>
<#assign version = 2>

<@header css js version/>
    <div id="app"></div>

    <script type="text/javascript">
        window.__INITIAL_STATE__ = {
            baseUrl: '${baseUrl}',
            lng: '${lng}',
            fallbackLng: '${fallbackLng}',
            isSuperUser: ${isSuperUser?c}
        }
    </script>

    <script type=text/javascript src="<@resource_href "/js/#new app name#/manifest.js"/>"></script>
    <script type=text/javascript src="<@resource_href "/js/#new app name#/vendor.js"/>"></script>
    <script type=text/javascript src="<@resource_href "/js/#new app name#/app.js"/>"></script>

<@footer version/>
```

You have to create a controller in ```src/main/java/org/molgenis/new/app``` with the following content:

```java
@Controller
@RequestMapping(URI)
public class NewAppController extends VuePluginController {
  private static final Logger LOG = LoggerFactory.getLogger(NewAppController.class);

  public static final String NEW_APP = "new-app";
  public static final String URI = PLUGIN_URI_PREFIX + NEW_APP;

  public NewAppController(
      MenuReaderService menuReaderService,
      AppSettings appSettings,
      UserAccountService userAccountService) {
    super(URI, menuReaderService, appSettings, userAccountService);
  }

  @GetMapping("/**")
  public String init(Model model) {
    super.init(model, NEW_APP);
    return "view-new-app";
  }
}
```

That is it.

## Guidelines
Below you can find some guidelines + code examples for stuff that we view is standard when creating a MOLGENIS plugin.

**Use Font awesome for icons[<sup>1</sup>](#guidelines-1)**


```html
<i class="fa fa-plus"></i>
```

**Write unit tests for mutations, actions, getters and other pure JS code**
```js

// utils.spec.js

import { swapArrayElements } from 'utils'

describe('swapArrayElements', () => {
    it('should swap the location of two objects in an array', () => {
        const array = [1, 2, 3, 4, 5]

        const actual = swapArrayElements(array, 2, 3)
        const expected = [1, 2, 4, 3, 5]

        expect(expected).to.deep.equal(actual)
    })
})
```

**Create named Vue components**
```js

// ComponentA.vue

<script>
    export default {
        name: 'component-A'
    }
</script>
```

**To allow theme changes to affect all specific color sets, use sass mixins and variables[<sup>1</sup>](#guidelines-1) when setting colors** 
```scss
<style lang="scss">
  @import "~variables";
  @import "~mixins";
  
   .some_class {
      background-color: $red;
    }
    
    .some_other_class {
      background-color: darken($red, 20%)
    }
</style>
```

**When using Vuex for your state, use TypeScript to add typing your state parameters[<sup>1</sup>](#guidelines-1)**
```js

// state.js
export type State = {
  message: ?string
  isUpdated: boolean
}

const state: State = {
  message: null,
  isUpdated: false
}
```

**Use i18n for labels**
```html
<button>{{ 'back-to-home-button' | i18n }}</button>
```

**When writing actions or mutations for your store, use constants for the different types**
```js

// mutations.js
export default {
    'setMessage' (state, message) {
        state.message = message
    }
}
```

More to come...







