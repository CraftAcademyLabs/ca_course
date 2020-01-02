## Setting up Continous integration with Semaphore

* Visit the Semaphore classic, make sure that you are not on Semaphore 2s website. https://semaphoreci.com/
![alt text](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/01_semaphore-pick-classic.png  "Logo Title Text 1")

* If you don't already have an account, set one up with Github

* Click the button to add a new project
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/02_semaphore-add-project.png)

* Find the repo (`rails_demo`) you want to add CI to in the list which shows your own repositories
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/03_semaphore-select-repo.png)

* When they ask you who is supposed to own this project, pick `Craft Academy` if you can.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/05_select-owner.png)

* After you selected the repo you want to use you should be able to set which branch we want CI for, usually the development branch but in your case we want to pick the master branch. The branch you will merge feature branches in to.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/04_semaphore-select-branch.png)

* After you have selected the branch and semaphore has analyzed the project, you are presented by some options.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/06_semaphore_analyzing-repo.png)

* You usually don't have to change the `Language` & `Ruby version`. Make sure that `database.yml for` option is set to `pg`.

* First off delete both of the jobs, we only want the setup.

* Edit the setup and put this in there

```
bundle install --deployment --path vendor/bundle
bundle exec rake db:setup
bundle exec rake db:test:prepare
COVERALLS_REPO_TOKEN=your_coveralls_token bundle exec rake ci:tests
```
If you are using Rails version 6 or above you should add this:
```
nvm install 13.1.0
nvm use 13.1.0
gem update --system
gem install bundler
bundle install --path vendor/bundle
yarn
bundle exec rails db:setup --all db:migrate
bundle exec rails db:test:prepare
COVERALLS_REPO_TOKEN=your_coveralls_token bundle exec rails ci:tests
```

* Make sure to get the repo token from coveralls project and reålace the placeholder in the code example.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/07_semaphore-project-settings.png) 

* Now go ahead and build with these settings.
![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/08_semaphore-build-with-settings.png)

You have now successfully added CI with semaphore to your project.

![](https://raw.githubusercontent.com/CraftAcademyLabs/ca_course/master/guides/coveralls-ci-cd/assets/09_semaphore-complete.png)

