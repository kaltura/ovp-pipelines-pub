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

### Main pipeline - player_cicd.yaml
This is the main workflow.\
For the Canary version the whole flow (CI\CD) will run. \
For prod version the workflow will run the CI without "build-uiconf" and "deploy-uiconf".

#### steps:
* accept-prod (triger)