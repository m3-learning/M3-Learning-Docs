# Setting up Packages with Pyscaffold

## Requirements

- tox
- pyscaffold

## Install pyscaffold and tox

```bash
pip install pyscaffold tox
```

## Build the Scaffold


In the repo 

```bash
putup ./ --force
```

## Copy the CI/CI action file

From the m3-learning-utils repo, copy the `.github/workflows` folder to the `.github/workflows` folder in the new repo.

## _static files

Copy the contents of the `./docs/_static` folder from the m3-learning-utils repo to the new repo.

## Copy the requirements.txt file

Copy the `./docs/requirements.txt` file from the m3-learning-utils repo to the new repo.

## Initial Commit

Make the initial commit

## Copy contents of the Config.py file

Copy the contents of the `./docs/Config.py` file from the m3-learning-utils repo to the new repo.

## Check all package specific changes

Go through the Diff and revert all package specific changes.

## First Build

add a git commit with the tag `#patch`
