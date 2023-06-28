# blue/green deployment for AWS project
## links
* [circleci-dashboard](https://app.circleci.com/pipelines/bitbucket/cherkavi-udacity)
* [slack setup authentication for application](https://github.com/CircleCI-Public/slack-orb/wiki/Setup)
* [slack usage](https://circleci.com/developer/orbs/orb/circleci/slack)
* [circleci e-mail notifications](https://app.circleci.com/settings/user/notifications)
* [circleci + slack](https://circleci.com/blog/circleci-slack-integration/)
  > exit 60 - issue with certificate, increate cimg/node version
  > right click on app, View app details, add this app to channel
* [circleci slack orb](https://github.com/CircleCI-Public/slack-orb)

```sh
udacity
cd udacity/hometask || { mkdir udacity/hometask; cd udacity/hometask; }
# git clone https://github.com/cherkavi/udacity-aws-devops-customization.git
# git remote add bitbucket https://vitalii_cherkashyn@bitbucket.org/udacity-aws-devops-customization
cd udacity-aws-devops-customization

# $BITBUCKET_PASSWORD
git pull bitbucket master
git push bitbucket master
```

## circleci
context: slack-config
```
```