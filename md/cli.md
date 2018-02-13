# Bonita Continuous Delivery CLI

This is a command-line tool for Bonita Continuous Delivery.

## Install

The CLI is provided as a Python package. Install the package locally as follows:

```
$ cd cli
$ pip install --user .
```

This will make the `bcd` command available under `$HOME/.local/bin`.

Basically make sure to add `$HOME/.local/bin` to your `PATH` environment variable in order to make your environment aware of the `bcd` command.


## Usage

The `bcd` command takes a mandatory option called `scenario` through the `-s / --scenario` command line option.

Then the `bcd` command provides the following subcommands in order to drive Bonita Continuous Delivery module:
```
Usage: bcd [OPTIONS] COMMAND1 [ARGS]... [COMMAND2 [ARGS]...]...

  Bonita Continuous Delivery CLI.

Options:
  -s, --scenario PATH    YAML scenario file (required)
  -y, --yes              Execute action without confirmation prompt
  -v, --verbose          Enable Ansible verbose mode
  --nocolor              Turn output colorization off
  -e, --extra-vars TEXT  Extra vars for Ansible (multiple) - Variables are
                         passed using the key=value syntax.
  -h, --help             Show this help message

Commands:
  status    Show the platform status
  create    Create AWS instances
  deploy    Deploy Bonita stack
  undeploy  Undeploy Bonita stack
  destroy   Destroy AWS machines (EC2 instances)
```

### Examples

```
$ bcd -s scenarios/uswest2_cluster.yml create

$ bcd -s scenarios/vagrant_single.yml -e mail_notification=no -y deploy

$ bcd -s scenarios/uswest2_performance.yml destroy --dry-run
```

### Multi command chaining

The BCD CLI allows to invoke more than one command in one go. This is useful to chain commands as a pipeline. If one command fails then `bcd` stops executing any subsequent command it may have.

For example:
```
$ bcd -s scenarios/uswest2_cluster.yml -y create deploy
```
