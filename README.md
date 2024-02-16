# IoT - Postspot

## Setup

### 1 .Node.js

The project uses [.nvmrc](https://github.com/nvm-sh/nvm#nvmrc) file, which contains an exact Node.js version number to be used across the project.

To install Node.js, run in the root of the project folder:

```shell
$ nvm install
```

To be sure you are using the correct Node.js version with the project, always run the following:

```shell
$ nvm use
$ node -v
v18.17.0
```

### 2. Turborepo

You can [install Turborepo](https://turbo.build/repo/docs/installing) globally to enable automatic workspace selection based on the directory where you run `turbo`.

```shell
$ npm install turbo --global
```

### 3. Install project dependencies

```shell
$ npm install
```

### 4. AWS CLI

To interact with AWS, you will need [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) installed (see [Installing or updating the latest version of the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)).

> If you are on _Mac OS_ you can also simplify your life using [Homebrew](https://brew.sh/) and run the following formula: [`brew install awscli`](https://formulae.brew.sh/formula/awscli).

Configure your _AWS CLI_ options using [`aws configure`](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/configure/index.html) command with the selected profile name (you will use it later to deploy the project resources on AWS):

```shell
$ aws configure --profile <PROFILE_NAME>
AWS Access Key ID [None]: accesskey
AWS Secret Access Key [None]: secretkey
Default region name [None]: eu-central-1
Default output format [None]: json
```

`<PROFILE_NAME>` is the name of the profile you want to create. It can be any name you want, but it is recommended using the name that indicates the client you are working with and AWS environment (for example, `iot-dev`).

You should always be aware of the profile (AWS environment) in use by defining the target profile with the `--profile` option, for example:

```shell
aws s3 ls --profile <PROFILE_NAME>
```

A better way is to set the `AWS_PROFILE` environment variable. This way, you don’t have to explicitly add the `--profile` option for every AWS CLI command.

```shell
$ export AWS_PROFILE=<PROFILE_NAME>
```

If you forget your profile name, you can always check it with:

```shell
$ aws configure list-profiles
```

### 5. AWS CDK

You will need to [bootstrap the AWS CDK](apps/infra/README.md#cdk-bootstrap) into your AWS environments for the fresh project and AWS accounts.

## Modules

```
iot
├── apps
│  ├── infra
│  ├── api
│  ├── site
│  └── e2e
├── lambdas
|  ├── lambda-tools
|  └── edge-origin-response
└── packages
   ├── eslint-config-dxstage
   ├── tsconfig
   ├── ui
   └── utils
```

[`apps/`](apps) - Applications

- [`infra/`](apps/infra/README.md) - CDK application for creating AWS infrastructure
- [`api/`](apps/api/README.md) - API application
- [`site/`](apps/site/README.md) - client site web application
- [`admin/`](apps/admin/README.md) - admin web application
- [`e2e/`](apps/e2e/README.md) - end-to-end tests

[`lambdas/`](lambdas) - Lambda functions

- [`lambda-tools/`](lambdas/lambda-tools/README.md) - shared tools for AWS Lambda functions
- [`edge-origin-request/`](lambdas/edge-origin-request/README.md) - Lambda@Edge function for rewriting HTML responses to support SPA client-side routing
- [`edge-origin-response/`](lambdas/edge-origin-response/README.md) - Lambda@Edge function for rewriting HTML responses to support SPA client-side routing
- [`basic-auth-authorizer/`](lambdas/basic-auth-authorizer/README.md) - Basic Auth Authorizer Lambda function used to protect API documentation endpoints

[`packages/`](packages) - Shared packages

- [`eslint-config/`](packages/eslint-config/README.md) - shared ESLint config files (see [ESLint in a monorepo](https://turbo.build/repo/docs/handbook/linting/eslint))
- [`tsconfig/`](packages/tsconfig/README.md) - shared TypeScript config files (see [TypeScript in a monorepo](https://turbo.build/repo/docs/handbook/linting/typescript))
- [`ui/`](packages/ui/README.md) - shared UI React components
- [`gql/`](packages/gql/README.md) - shared GraphQL client (generated typed queries, mutations, subscriptions and typed GraphQL resolvers)
- [`utils/`](packages/utils/README.md) - general purpose utilities

## Environments

### Environment variables

The project uses [dotenv-cli](https://github.com/entropitor/dotenv-cli) to load environment variables from `.env` file from the project's root.

You can set up the environment variables in the `.env` file based on the outputs of the `bin/infra-params.sh` (see also `.env.example` file for reference).

The `.env` file must NOT be version-controlled and included in the Git repository (.gitignore will guard you).

## Deployment

### Infrastructure

To deploy the AWS cloud infrastructure using [CDK](https://aws.amazon.com/cdk/), see [AWS Cloud Infrastructure](apps/infra/README.md).

### Deploy the API

To deploy the API server, see [API](apps/api/README.md).

### Deploy the web applications

To deploy the client site web application, see [Site](apps/site/README.md).

## Useful commands

- `npm run lint` or `turbo lint` - lint the code
- `npm run test` or `turbo test` - perform the unit tests (add `--continue` option if you want to prevent Turborepo exiting early on filing tasks)
- `npm run build` or `turbo build` - compile typescript to js
- `npm run build -- --graph` or `turbo build --graph` - generate a dependency graph for `build` command (see [`--graph`](https://turbo.build/repo/docs/reference/command-line-reference#--graph) for more options)
- `npm run dev` or `turbo dev` - serve the development environment
- `npm run clean` or `turbo clean` - clean the project (removes `node_modules`, `dist`, `.turbo`)
- `npm i <package> -w apps/<workspace>` - add/install a dependency named `<package>` from the registry as a dependency of your workspace `apps/<workspace>` (`<workspace>` can be workspace directory `apps/site`, or full package name `@iot/site`)
- `npm test -w <workspace>` - run all tests in `<workspace>` workspace
- `npm init -w packages/<package> --scope iot` - create a scoped package inside a `packages` workspace
- `npm install @iot/<package1> -w @iot/<package2>` - install a scoped package as a dependency of another scoped package

## Generators

To help increase coding productivity, we use Plop generators (see [`plopfile.js`](plopfile.js)) that allow us to automate the creation of repetitive code files and templates.
It generates boilerplate code such as templates, components, controllers, models, etc.
Plop offers a simple and efficient way to maintain code consistency and speed up development time by allowing developers to generate repeatable code quickly.
It also includes a handy CLI to automatically run Plop generators easily, making development workflows more efficient.

- `npm run plop cloud-stack` - creates a boilerplate code for the CDK stack in `apps/infra/` workspace

## NPM

To check if any module in a project is 'old' (this checks the registry to see if any installed packages are currently outdated):

```shell
$ npm outdated -l
```

Option `-l` will show whether each package is a `dependency` or `devDependency`.

All dependencies in your current project will be listed with their current, wanted, and latest versions.
The wanted version is the version that is safe to update to without checking for breaking changes.
It is calculated depending on how your dependency versions are declared in the package.json file,
but it usually does not include major changes.

You can update all dependencies to the wanted version using the following command:

```shell
$ npm update
```

Or you can update individual dependencies by specifying its name:

```shell
$ npm update <package>
```

To update a specific module in a project to the latest major version:

```shell
$ npm update <package>@latest
```

To update all the monorepo `package.json` files to the latest major version:

```shell
$ npx npm-check-updates -u --deep
# Only package.json files are modified.
# We need to run npm install to update your installed packages and package-lock.json.
$ npm install
```

> See also [Syncpack](#syncpack) tool below.

To run a security audit for all the packages (submits a description of the dependencies configured in your project
to your default registry and asks for a report of known vulnerabilities):

```shell
$ npm audit
```

Fix vulnerabilities:

```shell
$ npm audit fix
```

## Syncpack

To keep all the dependencies in sync across the monorepo, we use [Syncpack](https://github.com/JamieMason/syncpack) tool.

Syncpack with []`fix-mismatches`](https://jamiemason.github.io/syncpack/fix-mismatches) command ensures that multiple packages
requiring the same dependency define the same version.
In combination with hoisting, the `fix-mismatches` feature reduces the number of `node_modules` of a specific dependency to
one because for each different version created will not have an additional `node_modules` for that respective dependency.

We can list all of the packages in the monorepo and their versions, along with any version inconsistencies:

```shell
$ npx syncpack list
```

To list only packages with inconsistent versions:

```shell
$ npx syncpack list-mismatches
```

To make all the package versions consistent, do the following, which appears to change all versions to the greatest version found for each package:

```shell
$ npx syncpack fix-mismatches
$ npm install # to update your installed packages and package-lock.json.
```

## Resources

- [monorepo.tools](https://monorepo.tools/)
- [npm - workspaces](https://docs.npmjs.com/cli/v8/using-npm/workspaces)
- [Turborepo](https://turbo.build/repo)
- [Turborepo - Monorepo Handbook](https://turbo.build/repo/docs/handbook)
- [ESLint](https://eslint.org/)
- [Prettier](https://prettier.io/)
- [Husky](https://typicode.github.io/husky/)
- [Commitlint](https://commitlint.js.org/)
- [Commitizen](https://github.com/commitizen/cz-cli)
- [Semantic-release](https://github.com/semantic-release/semantic-release)
- [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/)
- [validate-branch-name](https://github.com/JsonMa/validate-branch-name)
- [Using Jest with monorepo](https://kulshekhar.github.io/ts-jest/docs/guides/using-with-monorepo)
- [Plop](https://plopjs.com/)
- [Awesome Plop](https://github.com/plopjs/awesome-plop)

# iot
# iot
