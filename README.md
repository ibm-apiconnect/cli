# API Connect v5 Getting Started: Developer Toolkit Command Line Interface

[Shane Claussen](mailto:claussen@us.ibm.com), Chief Architect, API Connect



# Introduction

The API Connect v5 developer toolkit delivers a command line interface
named `apic` to augment the toolset developers use to create and test
APIs to be run, managed, and secured by API Connect.  The `apic`
command set also provides capability to support devops oriented
engineering tasks (e.g. continuous integration and delivery).

To install and use the `apic` CLI you'll need to get a [Bluemix IBM
id](https://bluemix.net), provision [API Connect in
Bluemix (free)](https://new-console.ng.bluemix.net/catalog/services/api-connect),
and then install the [API Connect Developer
Toolkit](https://www.npmjs.com/package/apiconnect).  Once installed, type `apic          
-h` to see the overall help (after accepting the licensing agreement):

```
$ apic -h

Usage: apic COMMAND OPTIONS

  Options
  -h, --help        command usage
  -v, --version     toolkit version

Commands (type apic COMMMAND -h for additional help):

  Creating and validating artifacts
    config          manage configuration variables
    create          create development artifacts
    edit            run the API Designer
    validate        validate development artifacts

  Creating and testing applications
    loopback        create and manage LoopBack applications
    microgateway    create Micro Gateway applications
    start           start services
    stop            stop services
    logs            display service logs
    props           service properties
    services        service management

  Publishing to the cloud
    login           log in to an IBM API Connect cloud
    logout          log out of an IBM API Connect cloud
    organizations   manage organizations
    catalogs        manage catalogs in an organization
    publish         publish products and APIs to a catalog
    products        manage products in a catalog
    apps            manage provider applications
    drafts          manage APIs and products in drafts
```

All the commands use a **command:action** syntax (e.g. `apic
apps:publish`).  For the most popular commands, either the command or
action portion is optional to simplify usage.  For example:

- apic auth:login -> apic login
- apic local:create -> apic create
- apic products:publish -> apic publish
- apic products:list -> apic products

All of  the commands take a `-h/--help` option which provides the
command's usage and a handful of useful examples (e.g. `apic publish
-h`).  Some of the command usages are annotated with **Stability:
prototype** which indicates we are in the process of collecting
customer feedback on the command and it will likely evolve before it's
certified for production.



# API Designer

**API Designer** is the graphical design tool provided by the toolkit
to support most of the capablity available via the other command
lines.  Use the `apic edit` command to run the API Designer:

```
apic edit
```

Although `apic edit` is likely the most important command in the
toolkit, the intention of this article is to provide an overview of
the entire `apic` command set. That said, if this is your first time
using the toolkit, it's good to know there's a simple graphical
alternative to the CLI.



# APIs and Applications

The developer toolkit supports development of API proxies and API
implementations.  In this article we'll use the term **API** when
we're referring to the API proxy and the term **application** when
we're referring to the API implementation.  The developer toolkit
provides a first class integrated development environment for
developing APIs and applications using the [LoopBack
framework](https://docs.strongloop.com/display/APIC/Using+LoopBack+with+IBM+API+Connect).
LoopBack application projects can be created using the loopback
command:

```
apic loopback
```

If you haven't selected an interaction tier framework for developing
APIs and Microservices we suggest you take a hard look at LoopBack
which has a proven track record for enabling interaction tier
applications to be built quickly and easily.  However, the developer
toolkit was designed with polyglot in mind.  Thus, it can be used to
create APIs in a language independent manner using
[OpenAPI (Swagger)](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md)
to proxy to an existing backend implementation or it can be used to
augment applications developed in other languages/frameworks such as
[Express.JS](http://expressjs.com), Java,
[Swift](https://developer.ibm.com/swift/2016/02/26/video-replay-ibm-announces-new-swift-offerings-tools/),
Go, et al.

When a developer is using **LoopBack projects**, the toolkit supports
publishing the API to an [**API Connect
Catalog**](https://www.ibm.com/support/knowledgecenter/SSMNED_5.0.0/com.ibm.apic.apionprem.doc/conref_working_with_env.html)
which supports socialization via the developer portal and policy
enforcement via the gateway, and the application to an **API Connect
App** which provides Node.JS runtime capability.

Developers who want to proxy to existing backend services or who are
developing their applications using other languages or frameworks use
**OpenAPI projects** that support publishing the OpenAPI (Swagger)
definitions to **API Connect Catalogs**.



# Creating Development Artifact Definitions

Development artifacts (e.g. OpenAPI (Swagger) definitions, API product
definitions, LoopBack models, and LoopBack data sources) can be created using
the `apic create` command.  Use the following CLIs to create the different
definition types interactively:

```
apic create --type api
apic create --type product
apic create --type model         # Note: Create within a LoopBack application project
apic create --type datasource    # Note: Create within a LoopBack application project
```

Product and API definitions can also be created non-interactively by
providing the **--title** option (several values get defaulted from
the title that can be customized with additional options):

```
apic create --type api --title Routes
apic create --type product --title "Climb On"
```

It is also convenient to create the API and product definitions at the
same time:

```
apic create --type api --title Routes --product "Climb On"
```

Alternatively, you can create a couple APIs and then reference them
when you create a new product:

```
apic create --type api --title Routes
apic create --type api --title Ascents
apic create --type product --title "Climb On" --apis "routes.yaml ascents.yaml"
```



# Validating Development Artifact Definitions


Once you edit the development artifacts or right before you publish the
artifacts it's valuable to validate them:

```
apic validate routes.yaml                      # Validate an API
apic validate climb-on.yaml                    # Validate the product and APIs created above
apic validate climb-on.yaml --product-only     # Validate the product only (do not validate the referenced APIs)
```



# Updating LoopBack Applications

Once you've created a LoopBack application using `apic loopback` there
are a number of useful commands for updating the application.  These
should be run in the application's project directory.  Here's an
overview of those commands:

- **`apic loopback:boot-script`**: Create a new [boot scripts](https://docs.strongloop.com/display/public/APIC/Defining+boot+scripts).
- **`apic loopback:acl`**: Define an [access control list](https://docs.strongloop.com/display/public/APIC/Controlling+data+access) for accessing LoopBack models.
- **`apic loopback:middleware`**: Define and register [Express.JS middleware](https://docs.strongloop.com/display/public/APIC/Defining+middleware) to define the phase of execution.
- **`apic loopback:property`**: Add [additional properties](https://docs.strongloop.com/display/public/APIC/Customizing+models) to a Loopback model.
- **`apic loopback:remote-method`**: Add [remote methods](https://docs.strongloop.com/display/APIC/Using+LoopBack+with+IBM+API+Connect) to a LoopBack model.
- **`apic loopback:relation`**: Add [relationships](https://docs.strongloop.com/display/APIC/Creating+model+relations) between LoopBack models.
- **`apic loopback:export-api-def`**: Export an OpenAPI (Swagger) definition from LoopBack models.
- **`apic loopback:export-api-def`**: Export an OpenAPI (Swagger) and a product definition from LoopBack models.
- **`apic loopback:swagger`**: Generate a LoopBack application from an OpenAPI (Swagger) definition.

Note: These commands are annotated with **Stability: prototype**
because we are looking for feedback on them before we certify them for
production.



# Managing Services

The `services` command can be used to manage running processes for
testing APIs and applications.  For example, the following commands
can be used to create a LoopBack application project, start the Micro
Gateway and the Node.JS server running the LoopBack application, and
test the API and application using the endpoint exposed by the Micro
Gateway:

```
apic loopback # climbon project name/directory
cd climbon
# update API and/or application development artifacts
apic start
# test the API/application
# update API and/or application development artifacts
# restart the services (result is they run off the latest artifact definitions)
apic start
# test the API/application
apic stop
```

By default, the actions for the `services` command work on the project
or directory relative to where the command was executed enabling
services for multiple projects to be managed concurrently and
independently.

Here's a sampling of some useful actions for the services command:

```
apic services     # list the local running services (alias for services:list)
apic start        # start the local services (alias for services:start)
apic stop         # stop the local services (alias for services:stop)
apic stop --all   # stop all services across all projects
```

While running services and testing APIs and applications it's
typically useful to tail the Micro Gateway and Node.JS LoopBack logs
using the `apic logs` command.  Here's a couple examples:

```
apic logs                         # view the logs for the default service (alias for services:logs)
apic logs --service SERVICE_NAME  # use apic services to list the service names
```

The props command provides a mechanism for managing service
properties.  Here's a summary of how the props command can be used:

```
apic props                         # List properties for the default service (alias for props:list)
apic props --service SERVICE_NAME  # List properties for the SERVICE_NAME service
apic props:set NAME=VALUE          # Set a property on the default service
apic props:get NAME                # Get the property for the default service
apic props:delete NAME             # Delete the property for the default service
apic props:clear                   # Clear all the properties on the default service
```



# Configuration Variables

The `apic config` command provides global and project based
configuration variables.  These configuration variables will grow over
time, but currently they are used to provide the target catalog and/or
app for publishing APIs and applications.  Global configuration is
stored in ~/.apiconnect/config and project/directory level
configuration is stored in PROJECT_DIR/.apiconnect.

Let's take a look at the catalog and app configuration variables:

**catalog**: The catalog configuration variable should be set to the
URI of an API Connect catalog.  The value of catalog defines a default
catalog target for all commands managing catalogs.  The values defined
by the catalog's URI can be overridden using the --server,
--organization, and --catalog command line options.  The catalog URI
has the form:

`apic-catalog://MANAGEMENT_SERVER/orgs/ORGANIZATION_NAME/catalogs/CATALOG_NAME`

**app**: The app configuration variable should be set to the URI of
API Connect app.  The value of app defines the default app target for
all commands managing apps.  The values defined by the app's URI can
be overridden using the --server, --organization, and --app command
line options.  The app URI has the form:

`apic-app://MANAGEMENT_SERVER/orgs/ORGANIZATION_NAME/apps/APP_NAME`

The easiest way to determine the values for these configuration
variables is to sign in to the API Manager application of your
provisioned [Bluemix API Connect
service](https://new-console.ng.bluemix.net/catalog/services/api-connect)
or your on premises cloud, and click on the **link** icon of the
catalog or app you want to publish your API or application to.  The
dialog that appears will provide you with the identifier of the
catalog or app along with the appropriate config:set command.

Although setting these configuration variables is not absolutely
required, they simplify all the command actions that interact with API
Connect clouds by providing default values for the --server,
--organization, --catalog, and --app options.

Below is an example of using `apic publish` with and without the
catalog configuration variable begin set:

```
# Without
apic publish climb-on.yaml --server mgmnthost.com --organization climbon --catalog sb

# With
apic config:set catalog=apic-catalog://mgmnthost.com/orgs/climbon/catalogs/sb
apic publish climb-on.yaml
```

A portion of the default values provided by the catalog configuration
variable can also be overridden by explicitly providing one of the
standard options with a different value.  For example, the `apic
publish` command can override the catalog portion of the default to
target the Quality Assurance catalog by using the --catalog option:

```
apic publish climb-on.yaml --catalog qa
```

Don't forget about global configuration variables.  If you use the
same catalog as the default target for multiple projects set the value
globally:

```
apic config:set --global catalog=apic-catalog://mgmnthost.com/orgs/climbon/catalogs/sb
```



# API Connect Cloud Authentication

The `apic login` and `apic logout` (aliases for auth:login and
auth:logout) commands are used to manage your authentication
credentials for IBM API Connect clouds.  Unlike many systems, API
Connect enables you to be simultaneously logged into multiple clouds,
enabling you to easily publish and manage APIs and Applications to
disparate on premises and Bluemix targets.

Login supports both interactive and non-interactive modes.  To login
interactively:

```
$ apic login
Enter your API Connect credentials
? Server: us.apiconnect.ibmcloud.com
? Username: claussen@us.ibm.com
? Password (typing will be hidden) ****************
Logged into us.apiconnect.ibmcloud.com successfully
```

Scripted non-interactive login can be accomplished using the --server,
--username, and --password options.



# Publishing APIs

Publishing APIs to API catalogs in API Connect clouds enables those
APIs to be socialized via developer portals and secured by gateways.

API Connect defines the concept of an API product (or product for
short) that's used to compose APIs for publishing.  The product
enables API product managers to bundle one or more APIs together,
control the visibility of that product in the developer portal (ie only
allow partners x, y, and z view and subscribe to the product), and
defines one or more plans to provide application developers a set of
consumption options.  The products that reference the APIs and define
the consumption plans are also the primary unit of lifecycle
management for APIs.

The `apic publish` (alias for `apic products:publish`) command is used
to publish API products to an API Connect cloud.  The example below
demonstrates creation of a couple APIs composed by a product and how
to publish that product and its APIs to a catalog:

```
apic create --type api --title Routes
apic create --type api --title Ascents
apic create --type product --title "Climb On" --apis "routes.yaml ascents.yaml"
apic config:set catalog=apic-catalog://mgmnthost.com/orgs/climbon/catalogs/sb
apic login --username some-user --password some-password --server mgmnthost.com
apic publish climb-on.yaml
```

The **--stage** option can be tacked onto `apic publish` resulting in
the product being staged into a catalog instead of published (products
have the states of staged, published, deprecated, retired, or archived
in the catalog):

```
apic publish --stage climb-on.yaml
```



# Publishing LoopBack APIs and Applications

LoopBack application projects contain both APIs and applications.  The
same `apic publish` command described above is used to publish the
LoopBack APIs and a new command named `apic apps:publish` is used to
publish the LoopBack application.

When a LoopBack application project is created default API and product
definitions are added to the PROJECT_DIR/definitions directory.
Publishing the API and product artifacts is the same as any other set
of API and product artifacts with the notable difference that these
artifacts can be generated directly from the application's LoopBack
models.  Here's a sample CLI narrative for developing and publishing
LoopBack APIs to illustrate that scenario:

```
apic loopback # climbon project name/directory
cd climbon
apic create --type model           # as many times as required
apic loopback:property             # as many times as required
apic loopback:remote               # as many times as required
apic loopback:relation             # as many times as required
apic loopback:refresh              # (re)generate the product and API artifacts
apic config:set catalog=apic-catalog://mgmnthost.com/orgs/climbon/catalogs/sb
apic login --username some-user --password some-password --server mgmnthost.com
apic publish definitions/climbon-product.yaml
```

In addition to the LoopBack APIs, the LoopBack application itself must
also be published to an API Connect app which represents a Node.JS
runtime.  Adding the following two commands to the above set will
result in publishing the LoopBack application (Note: apps:publish must
be run from within the LoopBack application project directory
structure):

```
apic config:set app=apic-app://mgmnthost.com/orgs/climbon/apps/sb-app
apic apps:publish
```

If the LoopBack application is being published to Bluemix, the API
Connect app can optionally be created in the organization on the fly
as a side effect of apps:publish.  In that case, the app configuration
variable does not have to be set and the --app option on
`apps:publish` is not required.



# Managing API Products

The `apic products` and `apic apis` command sets can be used to manage
products and APIs that have been published to API Connect catalogs.
Here's a sample for how the `products` and `apis` commands can be used
for a cradle to grave lifecycle example:

```
apic config:set catalog=apic-catalog://mgmnthost.com/orgs/climbon/catalogs/sb    # set the default catalog
apic login --username some-user --password some-password --server mgmnthost.com  # login into the mgmnthost.com cloud
apic create --type api --title Routes --product "ClimbOn"                        # create the product and API
apic publish --stage climbon.yaml                                                # publish the product to staged status
apic products                                                                    # list the products in the catalog
apic products:get climbon                                                        # get the product's properties
apic apis                                                                        # list the APIs in the catalog
apic apis:get routes                                                             # get the API's properties
apic products:set climbon --status published                                     # publish the product (onlines the API)
apic apis:set routes --status offline                                            # take the API offline
apic apis:set routes --status online                                             # bring the API online
apic products:set climbon --status deprecated                                    # deprecate the product
apic products:set climbon --status retired                                       # retire the product
apic products:set climbon --status archived                                      # archive the product
apic products:delete climbon                                                     # delete the product from the catalog
```

Here's an example of a more complex lifecycle management scenario
where a new version of a product and API hot replaces the original
version:

```
# Set the default catalog and login to the mgmnthost.com API Connect cloud
apic config:set catalog=apic-catalog://mgmnthost.com/orgs/climbon/catalogs/sb
apic login --username some-user --password some-password --server mgmnthost.com

# Create and publish an initial version
apic create --type api --title Routes --version 1.0.0 --filename routes100.yaml
apic create --type product --title "Climb On" --version 1.0.0 --apis routes100.yaml --filename climbon100.yaml
apic publish climbon100.yaml

# Create a new version to fix a bug in the API, stage it to the catalog
apic create --type api --title Routes --version 1.0.1 --filename routes101.yaml
apic create --type product --title "Climb On" --version 1.0.1 --apis routes101.yaml --filename climbon101.yaml
apic publish --stage climbon101.yaml

# Inspect the catalog
apic products
apic products:get climbon:1.0.0
apic products:get climbon:1.0.1

# Hot replace version 1.0.0 with 1.0.1
apic products:replace climbon:1.0.0 climbon:1.0.1 --plans default:default
```

In addition to the lifecycle management capabilities, products and
apis in catalogs can be downloaded via the `pull` and `clone` actions:

```
apic products:clone               # download all products and their APIs from the catalog
apic products:pull climbon:1.0.0  # download the climbon:1.0.0 product and its APIs from the catalog
apic apis:clone                   # dwonload all APIs from the catalog
apic apis:pull routes:1.0.0       # download the routes:1.0.0 API from the catalog
```

It's also sometimes useful to clear all products and their APIs from a
catalog particularly for sandbox typed catalogs (this action requires
the name of the catalog as a confirmation parameter):

```
apic products:clear --confirm CATALOG_NAME
```



# Drafts

We encourage developers to co-locate their APIs and applications in
their local source code control systems to support the typical design
time iterative lifecycle activities like commits, branching, merges,
continuous integration, et al.  We believe the developer toolkit
provides the bridge from the developer's environment and the API
Connect runtime services.

That said, API Connect does provide an online development sandbox
capability named **Drafts** where API and product definitions can be
defined.  The toolkit provides the `apic drafts` command set to enable
synchronization of product and API artifacts between the developer's
local source code control systems and drafts.

Similar to the `products` and `apis` command sets, `drafts` can be
used to push, pull, and clone artifacts as follows:

```
apic config:set catalog=apic-catalog://mgmnthost.com/orgs/climbon/catalogs/sb    # set the default catalog
apic login --username some-user --password some-password --server mgmnthost.com  # login into the mgmnthost.com cloud

apic create --type api --title routes --product ClimbOn
apic drafts:push climbon.yaml                             # push climbon:1.0.0 and routes:1.0.0 to drafts
apic drafts                                               # list what is in drafts
apic drafts:get climbon                                   # get climbon:1.0.0
apic drafts:get routes                                    # get routes:1.0.0
apic drafts:pull climbon                                  # pull climbon:1.0.0 and routes:1.0.0 from drafts
apic drafts:clone                                         # pull every product/api from drafts
apic drafts:clear --confirm drafts                        # clear drafts collection
```

In addition to synchronizing data between the developer's source code
control systems and drafts, products that are in drafts can be
published:

```
apic config:set catalog=apic-catalog://mgmnthost.com/orgs/climbon/catalogs/sb    # set the default catalog
apic login --username some-user --password some-password --server mgmnthost.com  # login into the mgmnthost.com cloud

apic create --type api --title routes --product ClimbOn
apic drafts:push climbon.yaml                             # push climbon:1.0.0 and routes:1.0.0 to drafts
apic drafts:publish climbon
```

Again, although it's possible to develop products and APIs in drafts,
and to use the CLI or the drafts user experience to publish products
from drafts to a catalog, our recommendation is to clone all your
products and apis from drafts, check them into your local source code
control system, and then publish directly from there to your catalogs
using your source code control system, its tags, service branches, et
al, as your master of record.


# Summary

I hope you found the information above on the `apic` command useful!
Feel free to [contact me](emailto:claussen@us.ibm.com) to ask
questions or to provide feedback.



# Additional Resources

For additional resources pay close attention to the following:

- [API Connect v5 Getting Started: Developing, Testing, and Publishing an API to Bluemix using `apic`](https://github.com/ibm-apiconnect/climbingweather)
- [API Connect v5 Getting Started: API Products](https://github.com/ibm-apiconnect/product)
- [API Connect Developer Center](https://developer.ibm.com/apiconnect)
- [API Connect v5 Knowledge Center](http://www.ibm.com/support/knowledgecenter/SSMNED_5.0.0/mapfiles/ic_home.html)
- [Follow us @ibmapiconnect](https://twitter.com/ibmapiconnect)
