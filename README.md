# Git workflow help

Git workflow is how we describe a team's preferred processes for  how to branch, publish, revise, and maintain code.

Contents:

* [Quick start best practices](#quick-start-best-practices)
* [Steps](#steps)
* [Discussion](#discussion)
* [Release branch](#release-branch)
* [Release branch or release tag](#release-branch-or-release-tag)
* [Comments](#comments)
  * [Successful git-flow with small team and medical product](#successful-git-flow-with-small-team-and-medical-product)
  * [Merge from master back to your topic branch](#merge-from-master-back-to-your-topic-branch)
* [Merge vs. rebase](#merge-vs-rebase)
* [Git log as a history book](#git-log-as-a-history-book)
* [Git settings](#git-settings)
* [Feature flags](#feature-flags)


## Quick start best practices

Quick start for people who want the fastest-possible best practices for typical teams:

1. Aim for the master branch to always be working.
2. Do all development in topic branches: feature branches, bug fix branches, refactor branches, etc.
3. Use automation such as CI/CD to integrate topic branches into the master branch.

Development workflow:

1. To start a topic, pull the master branch, then create a topic branch.
2. To test a topic, push the topic branch to an automation process, such as CI/CD server.
2. To finish a topic, delete the topic branch locally and remotely.

Integration workflow:

1. When a topic branch is pushed, then the CI server starts to process the branch.
2. CI pulls the master branch then integrates changes into master as squash commits.
3. If all tests succeed, then the CI server pushes the master branch to remote.

Master breaks:

1. If master breaks in practice, or if master breaking could cost too much (because mistakes will happen), then use release branches.
2. If master breaks and you have time, then use a bugfix topic branch.
3. If master breaks and you're out of time, then use git revert.

Ideals:

1. Rapid integration is good.
2. Code review is good.
3. Git bisect is good.

Versioning:

* If your project is unversioned, then you may want to try simpler flows.
* If your project is versioned, then it's worthwhile to have any kind of git flow.

Topic branch:

* Do a refactor in a branch of its own, that doesn't add/remove any functionality.
* It's plausible that a new feature branch may have many related refator branches.
* Write a good git commit message: https://github.com/joelparkerhenderson/git_commit_message

Caveats:

1. If you have questions or ideas, please ask and comment here.
2. Showing actual commands is often helpful when describing git concepts.
3. If you're more advanced than this page, that's great, please let us know.


Git alias code:

    topic-start  = "!f(){ b=$1; git checkout master; git fetch origin master; git reset --hard origin/master; git checkout -b "$b" master; git push -u origin "$b"; };f"
    topic-update = "!f(){ git fetch origin master; git rebase origin/master; };f"
    topic-push   = "!f(){ b=$(git branch-name); git push -u origin "$b"; };f"
    topic-finish = "!f(){ b=$(git branch-name); git checkout master; git branch -d "$b"; git push origin ":$b"; };f"
    branch-name = rev-parse --abbrev-ref HEAD


## Steps

Get your master branch up to date:

    git checkout master
    git fetch origin master
    git reset --hard origin/master

  * This should never create a merge commit because we are never working directly in master.

  * Note: Whenever you perform a pull, merge, or rebase, run tests immediately after. This is because changes may not be compatible. 

Create your topic branch:

    git checkout -b add-foobar
    run tests

  * Choose a branch name using our guide.

  * Commit early and often, for instance whenever your tests pass.

If you want to push your work:

    git push -u origin add-foobar

If you want to rebase against the upstream to keep your branch mostly up to date:

    git fetch origin master
    git rebase origin/master

When you're ready to finish the topic, squash your topic branch commits:

    git fetch origin master
    git rebase -i origin/master
    git push


## Discussion

Examples:

* [A successful Git branching model - by Vincent Driessen](http://nvie.com/posts/a-successful-git-branching-model/)

* [What are the problems with 'a successful Git branching model'?](https://barro.github.io/2016/02/a-succesful-git-branching-model-considered-harmful/)

* [GitHub flow](https://guides.github.com/introduction/flow/)

* [GitLab flow](https://about.gitlab.com/2014/09/29/gitlab-flow/)

* [Atlassian simple git workflow](https://www.atlassian.com/blog/git/simple-git-workflow-simple)

* [A git workflow for agile teams](http://reinh.com/blog/2009/03/02/a-git-workflow-for-agile-teams.html)

* [Trunk-based development](https://trunkbaseddevelopment.com/)

Hacker news posts:

* https://news.ycombinator.com/item?id=1063198

* https://news.ycombinator.com/item?id=15376841

* https://news.ycombinator.com/item?id=1966820

Related ideas:

* Trunk based development

* Short lived topic branches a.k.a. feature branches, hotfix branches

* Centralized repostiroy vs. distributed repositories

* Using git tags and/or git branches

* Bug fixes, including mission-critical immediate-need bugs

* Sharing code among distributed developers while code is in progress

* Versioning

* Is any branch stable?

* Does "master" have special meaning because it is a git default name?

* Can a remote branch "foo" be different than a local  branch "foo"?

* When you're writing actual code, and type "git branch", what do you see?

Review ideas:

* Many small commits vs. one large commit

* Code reviews and/or human code quality assurance

* Signed code approvals, such as for audits or regulations

* Review processes such as Gerrit

Integration ideas:

* Continuous integration and/or continuous deployment

* Immediate integration vs. delayed integration

* Longer-lived approval processes e.g. by testers, regulators, lawyers

* Multiple simultanous production versions

Release ideas:

* Toggles such as feature flags, code gates, environment settings

* Release management e.g. green/blue, alpha/beta, canary/throttle

* How to manage APIs and other kinds of external public access?

* Scheduled releases and similar kinds of time locks

* Testing in production

Merge vs. rebase ideas:

* Using git merge and/or git rebase

* Linear history (e.g. rebase) vs. non-linear history (e.g. merge)

* Rebasing as a way to clean up history

* Git log as a human-friendly vs. machine-friendly

* Git log gardening a.k.a. hand-editing by a historian


## Release branch

I like to keep release branches to isolate the polishing of the release from other development work.

Without release branches, teams tend to have a code freeze, which is unnecessary with a release branch.


## Release branch or release tag

To mark a release, you could create a release archive, or release tag, or release branch.

GitHub automatically interprets tags created on the master branch as being releases, and even creates tar acrhive files with the branch's contents.


## Comments


### Successful git-flow with small team and medical product

By <a href="https://news.ycombinator.com/user?id=mkempe">mkempe</a>

I've used the git-flow approach successfully with a small team working on a medical product (so, embedded software system).

Eevery feature branch had to be reviewed before being merged with `develop`, which was submitted to nightly, extensive functional tests (initially one-hour long, eventually kept as a nightly subset of the more than 24-hours complete QA run) before it could be approved as a new (monthly) release and be merged with `master`.

Every new feature branch was automatically treated to quick continuous integration tests, and available for manually-triggered full functional tests (on the target devices).

This approach ensured that we had a full trace of development work, (signed) code reviews, and software changes -- compatible with FDA audits.

We also automated collection of code coverage data during functional tests, to inform analysis and revisions of the battery of functional tests.

To do signed code reviews, We used PRs with BitBucket for all code reviews. The reviewer(s) had to digitally sign their final approval of the review comments+answers and of the related code changes, if any.

The only way to merge a feature branch into `develop` was via the PR + code review process.

We used the BitBucket web interface to enter the approval message and click the approved button to allow for merge. These actions are recorded and visible in the overview page of the PR.

However the BitBucket server's web interface was not approved/validated for long-term storage and evidence for the audit trail, so the PR owner was responsible (before triggering the merge) for saving a PDF copy of that PR page and committing the PDF file into a git-controlled code-review directory.

So it's digitally signed to the extent that your account/identity is recorded in the approval step and in the collection of PDFs. I did ask about a more systematic export method but it was not considered important given the PDF-based approach.


### Merge from master back to your topic branch

By <a href="https://news.ycombinator.com/user?id=gregmac">gregmac</a>

This is how my team handles merge conflicts: your branch must cleanly merge for the pull request to be approved (which means you must resolve conflicts in your branch first). If there are two branches that may conflict in terms of functionality (but not at a source level) we call that out, and have the people involved reviewing both. When it comes time to merge, we usually merge the first one done to master, merge master to the second, then do extra testing in the second branch before merging it.

Sometimes if we know one branch blocks the other, we simply merge one to the other, but then still go to master separately. This makes the pull request review much simpler and keeps the code isolated while not duplicating effort. This works best when a big bug has a quick and simple but incomplete fix, and a more risky and complex but complete fix, or when there's multiple aspects to a new feature. We can decide to ship the first branch earlier if necessary.


## Merge vs. rebase

Q. Anyone want to weigh in on merge commits?

  * I've seen arguments in favor of using `git rebase` everywhere, both ways, and to never squash commits. This makes `git bisect` usable, since you never run into a situation where it points to a massive commit as the problem.

  * On the other hand, this seems pretty terrible from a `git log` perspective, since many commits are WIP. But maybe it's not a big deal. My bigger concern is that merge commits provide real context: whenever you merge a topic branch into master, it seems to make sense to have a merge commit for that entire operation. But wouldn't that cause `git bisect` to always point to that merge commit rather than one of the smaller commits?

A. I always advocate workflows where merges never happen. -[shandor](https://news.ycombinator.com/user?id=shandor)

  * To me, the 'git log' of a master branch is like a history book about the repository.

  * When you read through it, it should give you answers to the questions "what was changed, why, and when" in as clear format as possible.

  * Even though I always advocate rebasing, I also think that WIP commits should not reach master as-is. We are actually using Phabricator with my current team, and they have a really nice opinionated way of doing development. All dev happens usually in (really short-lived) task branches, but once they have gone through review etc. they are landed on master as one single commit, with the commit message holding all the relevant information about the change(s) made in the original task branch.

  * The reason for not having the WIP commits on master is that "commit early, commit often" is good practice, so the sheer amount of WIP commits will completely drown out the actual, finalised changes (i.e. the "actually interesting history as in a history book") in master. So no, I don't think squashing is a bad idea, I think is absolutely essential if you rebase onto master. If you don't squash, rebasing might lead to more mess than using merge.

  * Now, as to keeping WIP commits. I think that their value is usually much overestimated. I can count the times I have needed to go back to the actual, raw WIP commits instead of the properly rewritten one in master with one hand. But if you feel that it's the only thing keeping you from switching from merge-intensive flow to a always-interactive-rebase one, I'd encourage you just to retain the original, short-lived development branches in origin as separate branches. IMHO that gives best of the both worlds, if you think throwing out the WIP commits could hurt too much.

  * Having massive squashed commits should not usually be a problem because mostly they should not happen. Individual tasks should be so small that implementing them can not result in a massive amount of changed lines.

A. Use both.

  * Using interactive rebase lets you keep a clean and bisectable history made of small commits.

  * The developer every now and then can also rebase to master and ensure that all commits pass the tests (and perhaps write more tests based on what happened in the meanwhile on the master branch).

  * However, when CI runs, features are included in master with a merge commit, so that the occasional semantic merge conflict will bisect exactly to the merge commit and the developer of the feature isn't blamed incorrectly.


## Git log as a history book

Q. Should a git log be more like a history book with a readable narrative, or more like a computer log of actual steps?

A. I'm not sold on the idea of the master log as a readable narrative. -[mbrock](https://news.ycombinator.com/user?id=mbrock)

  * If you really want to maintain such a narrative, it would be possible to do it separately, in something like a changelog.

  * You might even hire someone to do that, a kind of technical historian. It's real work, because software development is pretty messy.

  * If you reaaally care about the history of the software's development, I would seriously consider aggressive rebasing of the repository even much later -- you could refactor the commit history as much as you want to clarify the logical progression of the software.

  * As I see it, a source code repository is not like a history book, because a history book is written after the fact by a trained historian who spends a lot of energy on tidying up the narrative and making it actually comprehensible.

  * A source code repository looks to me more like an archaelogical artifact with some terse notes sprinkled in there as clues by the various workers.

  * Basically I think the git log structure is kind of overblown and workflow arguments that hinge on the legibility of the repository's graph structure don't really matter that much to me.

  * I still sometimes write pretty involved commit messages, but that's a kind of separate issue from these "workflow" discussions that are mostly about how you should formally arrange the DAG. And I also know that my commit messages are mostly lost in time like tears in rain, so I try to communicate important changes in other ways.

A. This is a bizarre analogy.

  * History books are a record of things that happened in the world's timeline, which is in fact linear.

  * Source control is a record of things which happened in the timeline of development, which is typically branched.


## Git settings

To make `--no-ff` the default behaviour of git merge:

    git config branch.<branchname>.mergeoptions --no-ff


## Feature flags

Q. Who uses feature flags?

  * Facebook (extensively)

  * Etsy (extensively for many years)

  * ThoughtWorks (extensively on many projects)

Q. How do you prevent your codebase from becoming if-statement spaghetti?

A. You remove the feature flag checking code when you turn off or turn on the feature. 

  * It's an extra deploy, but it needs to be part of the prod/eng process. 

  * This is the most salient issue of feature flagging.

  * In my experience and something you need to get ahead of from the get-go.

A. When I use a feature flag as an alternative to branching, then I delete the if statements once the feature becomes permananent and gets released to all users. 

  * It's analogous to merging a branch.

  * Obviously I'd leave it there during QA and A/B testing, but once toggling is no longer necessary, I remove the toggle.

Q. How do you prevent new features from being leaked to the user? 

  * Details: They'll see the new features in the front end source code. 

    * At many companies this isn't an acceptable tradeoff. 

    * So do you preprocess the release code and strip out the disabled feature flags? 

    * With what?

A. You branch at the server/request level, not the client level.

  * So there is no set of features that gets displayed.

 * So the user only sees what the server returns.

Q. What do you use to control feature flags?

  * Maybe:

    * A json file filled with `"foo-bar-feature": true/false`?

    * Something more sophisticated, like a control panel?

    * A variable that can say "10% of our users will see this feature"?

A. You can use:

  * You can pay for a service like LaunchDarkly or Optimizely.

  * You can write your own service

  * You can have a file in memory; usually it starts simple with boolean toggles and evolves into a ramp up system that let's you selectively target users.

  * The important aspect is that you need to be able to change features without redeploying code.

A. Here are a couple of examples I use:

  * I use handlebars for my frontend templating. When I have a feature that needs to serve or withhold some HTML to/from the user, I put the feature flag in my JSON file of variables that get passed to handlebars. Handlebars has a way to wrap HTML with a boolean test.

  * I use the Google Closure compiler to optimize, uglify, and minify my code. When I need a feature flag that is code only, and I don't want users to accidentally get exposed, I use a boolean @define in JavaScript, and wrap feature code in an if statement. When the Boolean is false, Closure compiler removes that block from the output code.

  * I set up my own JSON file of config constants that are injected into the code during build. If I have a feature flag to A/B test something, or that I do want released to users but has a secret switch to enable, then I put the constant in the JSON file, and write UI code to toggle the flag. This one usually generates more code than the two above, but whenever I decide that the feature is either dead or permanent, I remove all the code to enable and disable the feature.

  * For fully dynamic toggling, I put my boolean config variable into my database. I use Firebase which is a giant pub-sub, so I wrap the toggle in a subscription to the database variable, which might then (for example) inject some HTML and/or toggle the CSS display attribute of some things on my page.


Q. Are feature flags really so nascent that there isn't a FOSS solution for it?

A. Feature flags have always been around, since long before branching.

  *  There isn't a FOSS solution for it separate from your framework(s) because how they work depends entirely on your stack.

  *  They're also normally really simple, adding the dependencies of another project just to have a boolean in your config file or in your code is usually overkill. 

  * That said, there are FOSS and commercial solutions for A/B testing, which is usually a feature flag workflow, but it comes with lots of other stuff you might not need, like the reporting, analytics and statistics parts.
