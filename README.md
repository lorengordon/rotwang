[![Build Status](https://travis-ci.org/plus3it/rotwang.svg)](https://travis-ci.org/plus3it/rotwang)

# rotwang
A framework for testing [salt](http://saltstack.com/) states and formulas.
Rotwang installs salt, configures salt for masterless operation, and executes
the equivalent of `salt-call state.show_sls <state>` and `salt-call state.sls
<state> test=True` on each state in the target formula. Rotwang will assert
whether each state renders without error.

Rotwang will read salt pillar files if they are present in the directory
`<formula>/tests/rotwang/pillar`. Rotwang executes the salt-call commands once
for each pillar file in the directory. This is equivalent to the commands
`salt-call state.show_sls <state> pillar='{<pillar>}'` and `salt-call
state.sls <state> test=True pillar='{<pillar>}'`. This allows rotwang to test
many combinations of formula configuration parameters.

# Setup

1. Create a directory in the formula root: `./tests/rotwang`.
2. Within that directory, add the directories:
    - `./pillar/`
    - `./salt/`
3. Populate the `pillar` directory with a pillar file for each combination of
configuration variables that you want to test.
4. Populate the `salt` directory with a `minion` configuration file containing
custom minion settings specific to the test environment. If this file is not
present, rotwang uses the [minimum configuration](TODO: add link) necessary
to execute salt in masterless mode. NOTE: If you specify a custom minion
configuration, it must at a minimum also include the settings from the default
rotwang configuration.

# Usage

## rotwang.py init <params>

`rotwang.py init` initializes the salt environment and prepares rotwang to 
execute the tests. It is required to execute `rotwang.py init` before testing
the formula. `rotwang.py init` accepts the parameters below. Parameters may be
passed on the command line, or set as environment variables. In the list
below, this is specifed in the form `[cmdline | env]`. Parameters passed on
the command line override those set in environment variables.

- `[--saltver | SALTVER]`: Version of salt to install. Rotwang uses the [salt
bootstrap](https://github.com/saltstack/salt-bootstrap) to install salt. The
value of this parameter is passed directly to the 'git' parameter of the salt
bootstrap installer, and so the parameter may set to any value accepted by the
bootstrap installer. This includes git branch names or git tags. (REQUIRED.)

- `[--saltsource | SALTSOURCE]`: Git url to the repo hosting the salt source.
(OPTIONAL. Defaults to `git://github.com/saltstack/salt.git`.)

## rotwang.py test <params>

`rotwang.py test` accepts the parameters below, and they must be passed on the
command line.

- `--path`: Path to the salt formula to test. (REQUIRED.)

# Examples:

```
git clone https://github.com/plus3it/rotwang.git
python ./rotwang/setup.py 
export SALTSOURCE=git://github.com/saltstack/salt.git
rotwang init --saltver=develop
rotwang test --path=/path/to/formula
```
