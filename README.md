---
marp: true
theme: default
---

# Bioinformatics session

A two-day workshop for bioinformaticians and molecular biologists with focus on the TSO500 pipeline in InPreD

![bg right](img/bergen01.png)

---

## Overview

1. Setup
2. Development & Collaboration
3. Nextflow
4. tso500_nxf_workflow
5. Python

---

## 1. Setup

---

## Create a GitHub account

- go to <https://github.com/> and click on `Sign up`

![](img/github01.png)

---

## Create a GitHub account

- enter your email

![](img/github02.png)

---

## Create a GitHub account

- set a password

![](img/github03.png)

---

## Create a GitHub account

- choose a username

![](img/github04.png)

---

## Create a GitHub account

- choose email preferences

![](img/github05.png)

---

## Create a GitHub account

- solve the puzzle

![](img/github06-07.png)

---

## Create a GitHub account

- create your account

![width:500px](img/github08.png)

---

## Create a GitHub account

- find the activation code in the email you received

![width:700px](img/github09.png)

---

## Create a GitHub account

- select the desired options

![](img/github10-11.png)

---

## Create a GitHub account

- choose the free plan

![height:450px](img/github12.png)

---

## 1. Setup

## Be added to InPreD organisation at GitHub

---

## 1. Resources

- [Getting started with your GitHub account](https://docs.github.com/en/get-started/onboarding/getting-started-with-your-github-account)

---

## 2. Development & Collaboration

### Short `git` introduction

- distributed version control system
- tracks history of changes commited by different contributors
- every developer has full copy of project and its history

---

### Short `git` introduction

#### Basic `git` commands

`git init`: initialises new git repository

`git clone <repository url>`: creates local copy of remote repository

`git add <file/s>`: stage new or changed files (anything that should be committed to the repository)

`git commit -m "feat: my new feature"`: commit changes to the repository

---

#### Basic `git` commands

##### commit message conventions

`<type>[optional scope]: <description>`

- `feat`: new feature
- `fix`: patching bug
- `refactor`: code change that neither is neither feat nor fix
- `build`: build system related changes
- `perf`: improving performance

---

##### commit message conventions

`<type>[optional scope]: <description>`

- `chore`: code unrelated changes, e.g. dependencies
- `style`: code change that does not change meaning
- `test`: changes to tests
- `docs`: adding/updating documentation
- `ci`: continuous integration, e.g. github actions

---

#### Basic `git` commands

`git status`: overview over untracked, modified and staged changes

`git branch`: show local branches

`git merge`: merge branches

`git pull`: load changes from remote counterpart

`git push`: upload changes to remote counterpart

---

## 2. Development & Collaboration

### Branching model: simplied Gitflow workflow

- start with two branches to record project history: `main` and `develop`
- each new feature resides in its own branch (feature branch)
- feature branch is generally created off latest `develop` commit
- upon feature completion, feature branch is merged into `develop`

---

### Branching model: simplied Gitflow workflow

![](img/gitflow01.png)

---

## 2. Development & Collaboration

### GitHub Actions

- continuous integration (CI) and continuous deployment (CD)
- building, testing and deploying directly from GitHub
- set up by adding yaml instructions to `.github/workflows`

```yaml
name: GitHub Actions Demo
on: [push]
jobs:
  Explore-GitHub-Actions:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Hello world!"
```

---

### GitHub Actions

```yaml
name: Docker Build
on:
  push:
    branches:
      - main
      - develop
    tags:
      - '*.*.*'

jobs:
  test:
    name: Run unit tests
    runs-on: ubuntu-latest
    steps:
      - 
        name: Check out the repo
        uses: actions/checkout@v4
      - 
        name: Unit testing 
        uses: fylein/python-pytest-github-action@v2
        with:
          args: pip3 install -r requirements.txt && pytest
  ...
  ```

---

### GitHub Actions

```yaml
...
  build:
    name: Build Image
    runs-on: ubuntu-latest
    needs: test
    steps:
      - 
        name: Check out the repo
        uses: actions/checkout@v4
      -
        name: Lint Dockerfile
        uses: hadolint/hadolint-action@v3.1.0
      - 
        name: Docker Meta
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: |
            inpred/local_app_prepper
          tags: |
            latest
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=semver,pattern={{major}}
      - 
        name: Login to Dockerhub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}
      - 
        name: Build and push image to Docker Hub
        uses: docker/build-push-action@v5
        with:
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```

---

### GitHub Actions

![width:900px](img/github_actions01.png)
![width:900px](img/github_actions02.png)

---

### GitHub Actions

![](img/github_actions03.png)

---

### GitHub Actions

![](img/github_actions04.png)

---

## 2. Development & Collaboration

### GitHub workflow

- go to issues and create a `New issue`

![width:900px](img/github_workflow01.png)
![width:900px](img/github_workflow02.png)

---

### GitHub workflow

- give the issue a descriptive title and a description and `Submit new issue`

![](img/github_workflow03.png)

---

### GitHub workflow

- if you decide to work on the issue (own repository), `Create a branch` via the issue

![](img/github_workflow04.png)

---

### GitHub workflow

- `Change branch source` to `develop` and `Create branch`

![width:800px](img/github_workflow05-06.png)

---

### GitHub workflow

- load the new branch to your local repository, check it out and start working
- push your changes back to the remote

```bash
$ git pull
$ git checkout 4-new-fancy-feature
$ git add README.md
$ git commit -m "docs: updating docs"
$ git push
```

---

### GitHub workflow

- for repositories you don't have access to, create a fork

![width:900px](img/github_workflow07.png)

---

### GitHub workflow

![width:400px](img/github_workflow08.png)

---

### GitHub workflow

- once you have a fork, `git clone` your forked repository
- create a new branch and work on that
- `git push` your changes back to the forked remote

---

### GitHub workflow

- when you are done, go to pull requests and create a `New pull request`

![width:900px](img/github_workflow09.png)
![width:900px](img/github_workflow10.png)

---

### GitHub workflow

- choose `develop` as `base` and your new feature branch for `compare`

![](img/github_workflow11.png)

---

### GitHub workflow

- `assign yourself`, add at least one reviewer (cog icon), provide some context and `Create pull request`

![](img/github_workflow12.png)

---

### GitHub workflow

- if you still want to work on the pull request, you can `Convert to draft` to let the reviewers know that it is not done yet
- otherwise you can just wait for them to review your changes

![](img/github_workflow13.png)

---

### GitHub workflow

- as a reviewer, make your you check your email notifications to see if there is pull requests waiting for you
- open the pull request and start the review in the `Files changed` tab

![](img/github_workflow14.png)

---

### GitHub workflow

- you can leave comments and suggestions in the code by hovering over the line with the changes and clicking on `+`

![](img/github_workflow15.png)

---

### GitHub workflow

- you can type your comment

![](img/github_workflow16.png)

---

### GitHub workflow

- or you leave a suggestion, ideally you click `Start a review` to initialise the reviewing process

![width:700px](img/github_workflow17.png)
![width:700px](img/github_workflow18.png)

---

### GitHub workflow

- when you are done with reviewing, `Finish your review`

![width:700px](img/github_workflow19.png)

---

### GitHub workflow

- again, leave a comment if you like, and choose if you just want to `Comment`, `Approve` or `Request changes`

![width:700px](img/github_workflow20.png)

---

### GitHub workflow

- you can add a general comment to the pull request under `Conversation`

![width:700px](img/github_workflow21.png)

---

- release and semantic versioning
- licensing

---

## 2. Resources

- [About Git](https://docs.github.com/en/get-started/using-git/about-git)
- [Gitflow workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [GitHub Actions](https://docs.github.com/en/actions/quickstart)

---

## 3. Nextflow

- general (install, best practice)
- nf-core template
- stubbing

---

## 4. `tso500_nxf_workflow`

- status
- demonstration 

---

## 5. Python

- general (best practice, cli)
- unit testing (pytest)

--- 

### Resources

- [github actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
- [nf-core](https://nf-co.re/)
- [pytest unittesting](https://www.datacamp.com/tutorial/pytest-tutorial-a-hands-on-guide-to-unit-testing)
- [semantic versioning](https://semver.org/)
