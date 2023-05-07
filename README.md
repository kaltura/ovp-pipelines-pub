# GitHub Actions For Players and Plugins CI/CD

This README will explain how the CI/CD for Players and Plugins V3 works.

## Introduction - high level

* Connectivity between Github and AWS by using OIDC on central
* [CI] Publish packages to NPM
* [CI] Upload js, js.map and translation to AWS S3
* [CI] Update schema for canary, qa
* [CI] Promote schema from qa till prod-latest
* [CI] Update versions on AWS SSM parameter store
* [CI] Update ini files for canary, beta, latest
* [CD] Update uiconf by using invoke AWS Lambda

## Usage

### Main pipelines (reusable of reusables) - 
1. player_cicd.yaml : \
    This is the main workflow.\
    For the Canary version the whole flow (CI\CD) will run. \
    For prod version the workflow will run the CI workflows without "build-uiconf" and "deploy-uiconf" from the list that mentioned below.

    #### Inputs
    
    | Name | Description | Type | Default | Required |
    |------|-------------|------|---------|:--------:|
    | [env]() | `env - example: 'qa/prod'` | `string` | `""` | no |
    | [type]() | `choose player or plugin or dependency - example: 'plugin/plugin'` | `string` | `""` | yes |
    | [stage]() | `build for specific type - example: 'canary/beta/latest'` | `string` | `""` | no |
    | [schema-type]() | `schema-type - example: 'playerV3Versions/playerV3OvpVersions'` | `string` | `""` | yes |
    | [yarn-upgrade-list]() | `list of packages to overwrite in package.json - example: 'playkit-js/playkit-js@canary @playkit-js/playkit-js-dash@canary'` | `string` | `""` | no |
    | [tests-yarn-run-to-execute]() | `list of package.json scripts - example: 'eslint flow test'` | `string` | `""` | yes |
    | [run-on-ubuntu]() | `run tests on ubuntu - example 'true/false'` | `boolean` | `ture` | no |
    | [run-on-macos]() | `run tests on ubuntu - example 'true/false'` | `boolean` | `false` | no |
    | [player-type]() | `player type - example 'ovp tv'` | `string` | `""` | no |
    | [enabled-openssl-legacy-provider]() | `enable the NODE_OPTIONS --openssl-legacy-provider - example: 'ture/false'` | `boolean` | `ture` | no |

    #### reusable worflows inside:
    * accept-prod (github environment for approve production)
    * [running-tests](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_tests.yaml)
    * [upload-packages-to-npm](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_upload_to_npm.yaml)
    * [upload-to-s3](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_upload_to_s3.yaml)
    * [update-schema](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_update_schema.yaml)
    * [build-uiconf](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_build_uiconf.yaml)
    * [deploy-uiconf](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_deploy_uiconf.yaml)
    * [notification](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/notification.yaml)


2. player_promote.yaml : \
   This workflow designed for schema and uiconf promotion in the following order: 
   - "qa --> qa-beta"
   - "qa-beta --> prod-beta"
   - "prod-beta --> prod-latest"

   #### Inputs

   | Name | Description | Type | Default | Required |
   |------|-------------|------|---------|:--------:|
   | [promotion-type]() | `from - to - example: 'qa --> qa-beta'` | `string` | `""` | yes |
   | [schema-type]() | `schema-type - example: 'playerV3Versions/playerV3OvpVersions'` | `string` | `""` | yes |
   | [uiconf-new-version]() | `not required - if no value is given it will increment the minor version - example 'from 1.00 to 1.01''` | `string` | `""` | no |

   #### reusable worflows inside:
    * [promote-schema](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_promote_schema.yaml)
    * [build-uiconf](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_build_uiconf.yaml)
    * [deploy-uiconf](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/player_deploy_uiconf.yaml)
    * [notification](https://github.com/kaltura/ovp-pipelines-pub/blob/master/.github/workflows/notification.yaml)
   
### Other workflows (reusable) -

