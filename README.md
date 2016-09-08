# euc2016-cool-demo

[![Software License (3-Clause BSD)](https://img.shields.io/badge/license-BSD%203--Clause-blue.svg?style=flat-square)](http://opensource.org/licenses/BSD-3-Clause)

A cool demo for EUC2016 showcasing Erlang microkernel powered by Rumprun
unikernel.

## Motivation

The idea is to demonstrate building a cool project from scratch (kind-of)
based on the Keynote by Fred Hebert at the
[Erlang User Conference 2019](http://www.erlang-factory.com/euc2016#speakers).
This project uses the rebar3 plugin
<https://github.com/neeraj9/rebar3_uerl_crud>
which is based on <https://bitbucket.org/ferd/rebar3_crud>. 

## The Workflow

The workflow of using this project is as follows, which will get up and
running with a working build of a restful webservice with all the nice
features it should have. You may choose to write custom implementations later
because this is just for the demo at present.

Lets build the ecosystem, which will give a working Erlang/OTP for host as
well as a unikernel image for the Erlang/OTP virtual machine.

    make -f Makefile.rumprun .rumprun_packages_built

The build/ path now as the relavent artifacts, which will be used subsequently
to create a Erlang microkernel ready to run your application (you'll create
now).

Lets get some environment variables setup so that the system will find
relevant artifacts from the build earlier.

    . setenv.sh

It's time to now create a sample CRUD project via a plugin mentioned earlier.
This command will replace some files present in this project, but that is
alright.

    rebar3 new crud-project -f

Its time to create a new CRUD handler to go along with it.

    rebar3 new crud-handler my_resource

This is a manual step, but now you'll have to add the new handler `my_resource`
into the router (or src/router.erl which is generated while creating the CRUD
project). So, the contents of src/router.erl will modify as follows:

```
    specs() ->
        [
            my_resource
        ].
```

Now that the core project and handlers are in place, lets build a release.

    rebar3 as prod tar -n crud

Note that the rebar3 `release` command ```rebar3 release -n crud``` is
unnecessary since the `as prod` will automatically build it.

You should see an artifact generated at
`_build/prod/rel/crud/crud-0.1.0.tar.gz`, which will be packaged into
an iso (ISO9660) useful for running into the Erlang microkernel as shown below.

    make -f Makefile.ukernel crud-0.1.0.iso

Lets run the virtual machine with the CRUD application now within the
Erlang microkernel.

    PATH=$(pwd)/build/rumprun/rumprun/bin:$PATH sh run-vm --virt=kvm --iso=crud-0.1.0.iso

## Thanks

Hope you had run running the project.

## Authors

* Neeraj Sharma {[github: neeraj9](https://github.com/neeraj9)}

