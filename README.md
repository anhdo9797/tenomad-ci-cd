# TENOMAD

## HOW TO BUILD REACT APP WITH GITHUB ACTIONS & JENKINS

#### Code base: [Corona React - Free Admin Template](https://github.com/BootstrapDash/corona-react-free-admin-template#corona-react---free-admin-template)

### Buid the project with Github Actions

#### Step 1: Creat workflow
- In the project root directory, creat the file `github/workflows/<name>.yml`
- In file `<name>.yml` creat workflow as https://github.com/anhdo9797/demo-ci-cd-web/blob/master/.github/workflows/deploy.yml

#### Step 2: Get FIREBASE_TOKEN
- [Install Firebase CLI](https://firebase.google.com/docs/cli)
- Login: `sudo firebase login:ci --no-localhost --debug` 

#### Step 3: Set FIREBASE_TOKEN in to the [secrets project](https://docs.github.com/en/actions/security-guides/encrypted-secrets)