# Test Travis CI

Project to test Travis CI to push contents from a `/documentation` folder to the wiki repository associated.

## Usage

1. Install Travis CLI

```bash
$ gem install travis
```

2. Include Travis configuration in repository `.travis.yml`

```yaml
language: generic
env:
  global:
    - USER="your username" # Remove it from an org repository and use Travis variable
    - EMAIL="your email"
    - REPO_SOURCE="repo_source_name"
    - REPO_DEST="repo_wiki_name"
    - GH_REPO_SOURCE="github.com/${USER}/${REPO_SOURCE}.git"
    - GH_REPO_DEST="github.com/${USER}/${REPO_DEST}.git"
script:
  - MESSAGE=$(git log --format=%B -n 1 $TRAVIS_COMMIT)
  - git clone https://${GH_REPO_SOURCE}
  - git clone https://${GH_REPO_DEST}
  - yes | cp -rf ${REPO_SOURCE}/documentation/* ${REPO_DEST}/
  - cd ${REPO_DEST}
  - pwd
  - git remote
  - git config user.email ${EMAIL}
  - git config user.name ${USER}
  - git add .
  - git status
  - git commit -m "${MESSAGE}"
  # ${USER} variable is included by default in Travis config
  - git remote add origin-wiki "https://${USER}:${GITHUB_TOKEN}@${GH_REPO_DEST}"
  - git push origin-wiki master
```

3. Create a Token in GitHub with **public_repository** access accessing here https://github.com/settings/tokens.
4. Login with Travis CLI.

```bash
# If you are using travis.org
$ travis login
# If you are using travis.com
$ travis login --pro
```

5. Add the GITHUB_TOKEN with the value obtained in step 3.

```bash
# If you are using travis.org
$ travis encrypt GITHUB_TOKEN="secretvalue" --add
# If you are using travis.com
$ travis encrypt --com GITHUB_TOKEN="secretvalue" --add
```

This will generate an output in your `.travis.yml` file like this:

```yaml
secure: '.... encrypted data ....'
```

6. Commit and push changes in the repository that contains the `/documentation` folder. From now on, Travis CI will push them into the wiki repository.
