# Example App for New Pipeline

## How this works

### Pipeline Actions

There are three parts (called actions) to any project: Deploy, Build, Test.

#### Deploy

The deploy action moves code from one point to another, and ensures that the entrypoint to the application is invoked correctly with proper context.

#### Build

The build action assembles an artifact of some kind. This artifact can be used for testing or can be produced as a byproduct of testing. The build action differentiates from the test action in that the build action produces an artifact as an output while test only produces a signal (pass or fail).

#### Test

The test action simply runs any CI processes necessary. This can be unit tests, integration tests, acceptance tests or any other kind of process that produces a signal of 0 (for pass) and non-zero (for fail).

### Layout

In order to differentiate and isolate resources and between actions, each action is given a top-level dotted directory: .build, .test, .deploy. The pipeline will use, as it's entrypoint, the file with the same name in each directory. This is not a design feature, but rather, a common sense default meant to give newcomers to a project a common starting place. 

Thus, for the Test Action, the entrypoint is expected to be .test/test. It is also expected that .test/test should be executable and should also have a "shebang" at the top.

### Defining Outcomes

For each of these stages, you will need to define the outcomes of each action by integration with the CI Provider. For example, if you were using Travis CI, you would do the following:

```yaml
language: python
python:
  - '3.5'
  - '3.4'
  - '2.7'
before_install:
  - ./.test/before_install.sh
script: ./.test/test
```

If I want to have something happen after the job passes I would simply add a file called .test/passed and include a shebang.

```yaml
language: python
python:
  - '3.5'
  - '3.4'
  - '2.7'
before_install:
  - ./.test/before_install.sh
script: ./.test/test
after_success: ./.test/passed
```

### Required Files

* .build/build
* .test/test
* .deploy/deploy

These files are considered "minimum" however, other reserved/expected files are:

```
.(action)/(action) -> Required
.(action)/passed
.(action)/failed
.(action)/always
```

## Philosophy

The overall philosophy of this instrumentation is to create a portable, simplified, and clear set of expectations about what it takes for a project to go through a full lifecycle (build/test/deploy). The other important thing to ask the very minimum in terms of requirements so that teams can own all facets of their pipelines. You can pretty much make the action / scripts do anything you want so long as:

1. build creates an artifact
2. test outputs a signal (0/non-zero)
3. deploy triggers a deployment (a change in running code in a given environment)

