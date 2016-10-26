# Command Central Project Automation Tool

Welcome to Command Central project automation tool (ANTCC)

The tool is based on convention-over-configuration approach
to define a typical Command Central based automation project structure.

## Requirements and Dependencies

* [Apache Ant 1.9.x](https://ant.apache.org/)
* [Java 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

## Quick Start

Short usage help

```
ant -f main.xml -p
```

More help:

```
ant -f main.xml -S
```

# Project Structure and Default Targets

Complete project structure looks like this:

```
build.xml

bootstrap
  default.properties
  other.properties

clients
  cc.properties
  other.properties

environments
   default
     env.properties
   other
     env.properties

templates
   template1
     template.yaml
   template2
     template.yaml

tests
  test1.xml
  test2.xml
```

Most folders and files are optional and each of them are described below.

## Build.xml

The project MUST have build.xml file in its root.

The build.xml content at a minimum MUST import main.xml from the ANTCC distribution.

```
<?xml version="1.0"?>
<project>
	<import file="antcc/main.xml" />
</project>
```

The import provides access to default ANTCC targets and is enough to start using
the project, however more often you will add your own automation targets to this
main build.xml script.

The build.xml is a standard Apache Ant script you can use all the facilities
that Ant provides for writing cross-platform automation scripts by leveraging

* Generic targets imported from ANTCC
* Any Command Central REST API available via CC CLI commands
* Any generic automation facilities provided by Ant tasks and libraries
* Any 3-rd party tooling that provide CLI or API

For GIT based automation projects it is recommended to include ANTCC as a submodule

```
git submodule add git@github.com:url_to_antcc.git antcc
```

If your project is not on GIT you can clone ANTCC project anywhere on the local
file system and reference it using relative or absolute path.

## Bootstrap

ANTCC tool provides generic targets to optionally download (internal SAG network only) 
and then bootstrap Command Central server, CC CLI or SPM.
Bootstrap folder is optional and allows to customize bootstrap parameters.

```
bootstrap
  default.properties
  custom.properties
```

Default targets provided by bootstrap plugin:

```
ant -f lib/bootstrap.xml -S

Optional parameter for any target:

-Dbootstrap=other                Bootstrap local Command Central server
                                 using defaults from 'bootstrap/default.properties'
                                 or custom 'bootstrap/other.properties'
Targets:

ant upgrade                      Upgrade local Command Central server
ant client                       Bootstrap local CC CLI (client)
ant agent                        Bootstrap local SPM (agent)
ant startcc                      Start local Command Central server
ant stopcc                       Stop local Command Central server
```

You MUST bootstrap at least 'client' if not complete Command Central server
local in order to execute other project targets.

## Clients

After Command Central server or client is bootstrapped the default client
configuration is available for the current user, typically in ~/.sag/cc.properties file.

This configuration is used by default.

If you want your project to use a different client configuration file make a copy of
the configuration file and store it as client/default.properties.

You can help as many client configuration files as you need.

```
clients
  default.properties
  custom.properties
```

Default targets provided by sagcc plugin:

```
ant -f lib/sagcc.xml -S

Optional parameter for any target:

-Dcc=other                       Connection to Command Central server
                                 using global settings in '~/.sag/cc.properties'
                                 or project defaults from 'clients/cc.properties'
                                 or custom 'clients/other.properties'
Targets:

ant restartcc                    Restart local or remote Command Central server
ant jobs                         List recent jogs
ant killjobs                     Cancel active and remove completed jobs
ant log                          Show tail of Command Central default log
ant logs                         Show tail of agents default log
```

## Environments

Project can operate with a single default environment or multiple environments.

The default environment configuration is stored as 
environments/default/env.properties file.

For additonal environments create a separate folder and place env.properties
file there. The final structure looks like this:

```
environments
  default
    env.properties
  other
    env.properties
```

After the environments are defined you can apply existing templates 

Default targets provided by sagenv plugin:

```
ant -f lib/sagenv.xml -S

Optional parameter for any target:

-Denv=other                      Environment configuration to use from
                                 project defaults in 'environments/default/env.properties'
                                 or custom 'environments/other/env.properties'
Targets:

ant apply -Dalias=template       Apply template using its alias
```

## Templates

Project normally provide custom templates, at least one.

The templates are placed under templates/ folder, each template in a separate
folder. The template folder must have at least template.yaml file but may provide
any additional files to package with the template.

```
templates
   template1
     template.yaml
   template2
     template.yaml
     otherfolder
       otherfile
```

After the template(s) have been created they can be imported and applied.

Default targets provided by sagenv plugin:

```
ant -f lib/sagenv.xml -S

Optional parameter for any target:

-Denv=other                      Environment configuration to use from
                                 project defaults in 'environments/default/env.properties'
                                 or custom 'environments/other/env.properties'
Targets:

ant up                           Import and apply all project 'templates/*'
ant apply -Dalias=template       Apply template using its alias
ant import -Dt=templateFolder    Reimport template from source folder by do not apply
ant apply -Dt=templateFolder     Reimport and apply template from source folder
ant migrate -Dt=templateFolder   Reimport and migrate using template from source folder
```

## Tests

Your project should have tests. 

ANTCC allows you easily to define tests using [AntUnit](https://ant.apache.org/antlibs/antunit/) framework.

Create AntUnit tests and save them as tests/test*.xml files.

```
tests
  test1.xml
  test2.xml
```

Default targets provided by sagtest.xml plugin:

```
ant -f lib/sagtest.xml -S

Optional parameter for any target:

-Dsuite=some                     Run only tests from 'tests/some/*' folder

Targets:

ant test                         Run tests/test*.xml AntUnit tests

```

# Other Links

See Command Central Template SDK
