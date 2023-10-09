A git post-checkout hook that rollsback any DB changes that exist between branches (any non-commited changes will still persist) for Ruby on Rails

You may also want to check out: [https://github.com/tpope/hookup](https://github.com/tpope/hookup)

### Requirements

*  Some version of Rails (over version 2.3 tested)
*  This file, put inside your ```.git/hooks/``` folder

### WARNING -- WARNING -- WARNING

I tried to make this hook as rails version agnostic as possible. What this means is that it assumes no gems outside of the default. Unfortunately, that means that when you switch branches, if you entered any data in columns/tables that were added in that branch, it will roll those columns/tables back, meaning all your data you entered in those new areas will be gone. This script could be "fixed" to save your data as well if you include the gem [https://github.com/ludicast/yaml_db](https://github.com/ludicast/yaml_db) in your Gemfile, and then added functionality to backup on branch switch, then import when switching back the branch and then delete the file with imported data to keep things clean.

### Grumblings

I believe this would have really worked best with a pre-checkout hook, if it existed (I don't believe it does). Right now I deal with a post-checkout hook that checks out the new branch, pulls the db/migrate folder from the branch you just switched from, does the rollbacks, and then does a git-clean to get rid of the pulled db/migrate folders. With a pre-checkout hook, I wouldn't have to potentially mess up a new branch pulling in files from another branch, I could just do the rollback, then have it switch the branch. 


#### Final Comments

I'm sure some of you will look at this and cry out in pain with how awful the code is. I don't do bash scripting very often, so I'm sure you will be quite right. If you see problems, feel free to submit a pull request and I'll pull it in.

Example usage:

```bash
git checkout -b "new-feature-branch"
```

```ruby
rails generate migration AddEmailToUsers email:string #Or whatever migration/addition you are doing (models, etc)```
rake db:migrate # Check database to make sure your changes/tables were added
```

```bash
git add .
git commit -am "Commiting changes to this branch before switching back"
git checkout master
# [...] Changes should be rolled back when checking the db
````
