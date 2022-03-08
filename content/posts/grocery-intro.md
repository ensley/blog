---
title: "End-to-End Machine Learning with AWS"
date: 2022-03-07T21:52:23-07:00
draft: true
---

# Setting up the development environment

## Things to install

### Poetry

[Official docs](https://python-poetry.org/docs/master/#installation)

```shell
$ curl -sSL https://install.python-poetry.org | python3 -
```

Try running `poetry --version`, if that doesn't work you probably need to add to your
PATH. Open `~/.bash_profile` and add the line

```shell
$ export PATH="$HOME/.local/bin:$PATH"
```

### AWS CDK

[Official installation docs](https://docs.aws.amazon.com/cdk/v2/guide/getting_started.html#getting_started_prerequisites)

You will obviously need an AWS account first.

Install [node.js](https://nodejs.org/en/download/). This is required for the CDK libraries
to work, regardless of the language you'll actually be working in.

Install [the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
and [configure it](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html)
by running `aws configure`. If you do not have an access key for your account, [create one
by following the instructions here](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-creds)
and supply it when prompted.

Install the CDK:

```shell
$ npm install -g aws-cdk
```

Verify that the installation worked:

```shell
$ cdk --version
```

[Bootstrap your environment](https://docs.aws.amazon.com/cdk/v2/guide/cdk_pipeline.html#cdk_pipeline_bootstrap).
The instructions on that page show you how to do the bootstrapping with extra administrative
privileges, which are necessary for taking advantage of CDK pipelines. CDK pipelines make
life a little easier when it comes to automatically deploying apps on git commits, but
apps will work just fine without it. To bootstrap with extra permissions:

```shell
$ export CDK_NEW_BOOTSTRAP=1 
$ npx cdk bootstrap aws://ACCOUNT-NUMBER/REGION \
      --cloudformation-execution-policies arn:aws:iam::aws:policy/AdministratorAccess \
      aws://ACCOUNT-ID/REGION
```

To bootstrap without extra permissions:

```shell
$ cdk bootstrap aws://ACCOUNT-NUMBER/REGION
```

You can easily find your AWS account number if you set up the AWS CLI correctly:

```shell
$ aws sts get-caller-identity
```

And same goes for the default region you chose:

```shell
$ aws configure get region
```

## Creating the project

### Create project folder

From the folder where you want the project folder to be contained, run

```shell
$ poetry new --src grocery_sales
```

That creates this folder structure:

```
grocery_sales
├── pyproject.toml
├── README.rst
├── src
│   └── grocery_sales
│       └── __init__.py
└── tests
    ├── __init__.py
    └── test_grocery_sales.py
```

### Get the CDK ready

The documentation would have you believe the only way to initialize a project with the
CDK is to run something like

```shell
$ cdk init app --language python
$ source .venv/bin/activate
$ python -m pip install -r requirements.txt
```

The problem is that `cdk init` is pretty opinionated with respect to how your project
should be structured. For instance, it automatically sets you up with a virtual environment
created by [`venv`](https://docs.python.org/3/tutorial/venv.html). That's fine, but I'd
rather use Poetry for this project, and it's pretty easy to just add the correct dependencies
ourselves, which is about all `cdk init` really does for you. So with that said, we're
going to skip it and do this instead.

First we'll tell Poetry about the dependencies we need:

```shell
$ poetry add aws-cdk-lib@^2.15.0
$ poetry add "constructs>=10.0,<11.0"
$ poetry add --dev pytest@^7.0
```

Then we'll create a bare-bones CDK application file at `src/app.py`:

```python
#!/usr/bin/env python3
import os
import aws_cdk as cdk
from constructs import Construct

class PipelineStack(cdk.Stack):
    def __init__(self, scope: Construct, construct_id: str, **kwargs):
        super().__init__(scope, construct_id, **kwargs)

app = cdk.App()
env = cdk.Environment(
    account=os.getenv("CDK_DEFAULT_ACCOUNT"), region=os.getenv("CDK_DEFAULT_REGION")
)
PipelineStack(app, "PipelineStack", env=env)
app.synth()
```

This doesn't really do anything right now, and it will become clear later why we named
the class `PipelineStack`. The last thing to set up is a `cdk.json` file at the root of
the project folder.

```json
{
  "app": "python3 src/app.py"
}
```

This tells the CDK how to run our project. Notice that we had to specify `src/app.py`
because we're using the `src` project layout.

Now run `cdk synth` and you should see a CloudFormation template printed to the console.

Running `cdk diff` shows you what will be created and destroyed from the existing app
once the newly synthesized template is deployed.

Running `cdk deploy` will deploy those changes.

That's the basic workflow. Anytime you want to make changes to your app, you would do

```shell
cdk synth
cdk diff
cdk deploy
```

Technically you could just do the last step, since `cdk deploy` will also synthesize first.
But it's a good idea to look at the changes first before committing to them.

To get rid of all the resources created by the app, run

```shell
cdk destroy
```

However, note that there are certain resources that are not deleted by default when you do
this, but rather are "orphaned" and their association with the CloudFormation stack is removed
instead. S3 buckets are an example of such a resource - CloudFormation won't delete stored
data unless you explicitly tell it to.
