### dbt

A data build tool

#### installation

```bash
› pip install dbt
```

#### configuration
  To create your first dbt project, run:
  ```bash
  › dbt init [project]
  ```
  This will create a sample dbt_project.yml file in the [project] directory with everything you need to get started.

  Next, create a `profiles.yml` file in the `~/.dbt` directory. If this directory doesn't exist, you should create it. The
  `dbt_project.yml` file should be checked in to your models repository, so be sure that it does *not* contain any database
  credentials! Make sure that all of your private information is stored in the `~/.dbt/profiles.yml` configuration file.

##### example dbt_project.yml
```yml

# configure dbt file paths (relative to dbt_project.yml)

# the package config is _required_. If other packages import this package, 
# the name given below is used to reference this package
package:
  name: 'package_name'
  version: '1.0'

source-paths: ["models"]   # paths with source code to compile
target-path: "target"      # path for compiled code
clean-targets: ["target"]  # directories removed by the clean task
test-paths: ["test"]       # where to store test results

# default paramaters the apply to _all_ models (unless overriden below)
model-defaults:
  enabled: true           # enable all models by default
  materialized: false     # If true, create tables. If false, create views

# custom configurations for each model. Unspecified models will use the model-defaults information above.

models:
  pardot:                 # assuming pardot is listed in the models/ directory                   
    enabled: false        # disable all pardot models except where overriden
    pardot_emails:        # override the configs for the pardot_emails model
      enabled: true       # enable this specific model
      materialized: true  # create a table instead of a view
```

##### example ~/.dbt/profiles.yml
```yml
user:                         # you can have multiple profiles for different projects
  outputs:
    my-redshift:              # uniquely named, you can have different targets in a profile
      type: redshift          # only type supported
      host: localhost         # any IP or fqdn
      port: 5439
      user: my_user
      pass: password
      dbname: dev
      schema: my_model_schema # the schema to create models in (eg. analyst_collective)
  run-target: my-redshift     # which target to run sql against
```

#### use

`dbt compile` to generate runnable SQL from model files

`dbt run` to run model files on the current `run-target` database

`dbt clean` to clear compiled files

#### docker

an alternate means of using dbt is with the docker image jthandy/dbt. if you already have docker installed on your system, run with:
```docker run -v ${PWD}:/dbt -v ~/.dbt:/root/.dbt jthandy/dbt /bin/bash -c "[type your command here]"
this can be run from any dbt project directory. it relies on the same configuration setup outlined above.

on linux and osx hosts, running this can be streamlined by including the following function in your bash_profile:
```
function dbt() {
    docker run -v ${PWD}:/dbt -v ~/.dbt:/root/.dbt jthandy/dbt /bin/bash -c "dbt ${1}"
}
```

at that point, any dbt command (i.e. `dbt run`) into a command line will execute within the docker container.

#### troubleshooting

If you see an error that looks like
> Error: pg_config executable not found

while installing dbt, make sure that you have development versions of postgres installed

```bash
# linux
sudo apt-get install libpq-dev python-dev

# osx
brew install postgresql
```

#### contributing

From the root directory of this repository, run:
```bash
› python setup.py develop
```

to install a development version of `dbt`.
