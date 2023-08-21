# GitHub Actions make a CICD pipeline

## start to work
```sh
# copy project from https://github.com/udacity/cd12354-Movie-Picture-Pipeline
# destination point is: https://github.com/cherkavi/udacity-github-cicd
project 
cd udacity/hometask
cd udacity-github-actions
project_folder=`pwd`
alias project='cd $project_folder'

alias github='x-www-browser https://github.com/cherkavi/udacity-github-cicd'
alias github-actions='x-www-browser https://github.com/cherkavi/udacity-github-cicd/actions'

# open current readme file
code README-hometask.md
# open project instruction
x-www-browser https://learn.udacity.com/nanodegrees/nd9991/parts/cd12354/lessons/616ed4d7-a5ad-43cc-a147-80ee24bad75f/concepts/616ed4d7-a5ad-43cc-a147-80ee24bad75f-project-rubric
# open github workflows
x-www-browser https://github.com/cherkavi/udacity-github-cicd/actions
```

## TODO Not Clear ( leftovers )
* https://learn.udacity.com/nanodegrees/nd9991/parts/cd12354/lessons/616ed4d7-a5ad-43cc-a147-80ee24bad75f/concepts/11e5f0c7-e28b-4c2f-9c75-27af0ab3a79a
  4. Create a CloudFormation template to create the necessary resources, or use a provided Terraform manifest to create the resources. The Terraform files have been provided in the Project Workspace setup directory.


## [local configuration](https://learn.udacity.com/nanodegrees/nd9991/parts/cd12354/lessons/616ed4d7-a5ad-43cc-a147-80ee24bad75f/concepts/138b0a1d-3cc7-4316-a037-302221f8949a)
### check local toolset
```sh
jq --version
aws --version 
pip --version
helm version
kubectl version --short
docker --version
kubectl
pipenv 
nvm
tfswitch
kustomize --version
```

## Links
* [what to do](https://learn.udacity.com/nanodegrees/nd9991/parts/cd12354/lessons/616ed4d7-a5ad-43cc-a147-80ee24bad75f/concepts/11e5f0c7-e28b-4c2f-9c75-27af0ab3a79a)
* [init project with description](https://github.com/udacity/cd12354-Movie-Picture-Pipeline)
* [local env setup](https://learn.udacity.com/nanodegrees/nd9991/parts/cd12354/lessons/616ed4d7-a5ad-43cc-a147-80ee24bad75f/concepts/138b0a1d-3cc7-4316-a037-302221f8949a)
* [examples of pipeline](https://learn.udacity.com/nanodegrees/nd9991/parts/cd12354/lessons/a4eeb900-22de-4407-8a43-8d28579c55d2/concepts/3c636c96-31cc-4e4d-acba-4bfc7af07e5c)

