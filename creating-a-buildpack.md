# Creating a Cloud Native Buildpack

This is a step by step tutorial for creating a Ruby Cloud Native Buildpack. 

Before we get started make sure you have the following installed on your system 
- [Docker Community Edition](https://store.docker.com/search?type=edition&offering=community)
- [pack](https://github.com/buildpack/pack/releases)   

### Setup Your Local Environment

First we will want to clone a sample ruby app that you can use when developing the ruby cloud native buildpack

```
cd ~
git clone <path to sample ruby app>
```

Next we want to create the directory where you will create your buildpack

```
mkdir ~/ruby-cnb
```

### Create the Building Blocks of a Cloud Native Buildpack 

Now we will setup the buildpack scaffolding. You will need to make these files in your `ruby-cnb` directory

```
cd ~/ruby-cnb
```

#### buildpack.toml
Once you are in the directory. You will need to create a `buildpack.toml` file in that directory. This file must exist in the root directory of your buildpack so the `pack` cli knows it is a buildpack and it can apply the build lifecycle to it.  

Create the `buildpack.toml` file and copy the following into it 

```
#Buidpack ID and metadata
[buildpack]
id = "io.buildpacks.samples.ruby"
version = "0.0.1"
name = "Ruby Buildpack"

#Stack the buildpack will work with
[[stacks]]
id = ["io.buildpacks.stacks.bionic"]

```

You will notice two specific fields in the file: buildpack ID and stack ID. The buildpack ID is the way you will reference the buildpack when you create buildpack groups, builders, etc.  The stack ID is the root file system in which the buildpack will be built.  This example is bulit on ubuntu bionic.

#### Detect and Build 

Next you will need to create the detect and build scripts.  These files must exist in a `bin` directory in your buildpack directory.

Create your `bin` directory and the change to that directory.

```
mkdir bin
cd bin
```

Now create your `detect` file in the `bin` directory and copy in the following content

```
#!/usr/bin/env bash
set -eo pipefail

exit 1
```

Now create your `build` file in the `bin` directory and copy in the following content

```
#!/usr/bin/env bash
set -eo pipefail

echo "---> Ruby Buildpack"
exit 1
```

You will need to make both of these files executable, so run the following command.

```
chmod +x detect build
```

These two files are now executable detect and build scripts.  Now you can run your use your buildpack.

#### Using your buildpack with `pack`

In order to test your buildpack, you will need to run the buildpack against your sample ruby app using the `pack` cli.

Run the following pack command

```
pack build test-ruby-app --buildpack ~/ruby-cnb --path ~/ruby-sample-app 
```

The `pack build` command takes in your buildpack directory as the `--buildpack` argument and the ruby sample app as the `--path` argument

After successfully running the command you should see the following output. You should see that it failed to detect because the detect script was setup to fail 

```
2018/10/16 09:59:00 Selected run image 'packs/run' from stack 'io.buildpacks.stacks.bionic'
*** DETECTING:
2018/10/16 14:59:04 Group: Ruby Buildpack: error (1)
2018/10/16 14:59:04 Error: failed to detect
Error: run detect container: failed with status code: 6
```

