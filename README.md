# Git & Modularization Workflow

- [Ground Rules](#ground-rules)
- [Versioning](#versioning)
- [Adding a Dependency to an Application](#add-dep-to-app)
- [Use a Dependency in your Application](#use-dep-in-app)
- [Adding a Dependency to a Module](#add-dep-to-module)
- [Use a Dependency in your Module](#use-dep-in-module)
- [Modify a Dependency you own](#modify-owned-dep)
- [Modify a Dependency you don't own](#modify-unowned-dep)
- [Making a release](#making-releases)
- [Updating a Dependency](#updating-dep)
- [Q & A](#q-and-a)

## Terms
- Team: A group that is responsible for a limited number of sections in an overall application.
- Semver: See http://semver.org
- Module: A unit of code in the form of a library or framework.
- Organization: A GitHub organization that groups a collection of repositories together

#### A word about Teams

A team can be divided up however you like.  The idea is to figure out the lines of responsibility such that only those responsible for a given piece have write access to it, and any outside changes come in the form of PRs.

<a name="ground-rules"></a>
## Ground rules

- Only applications should have submodules.
- Modules/Frameworks should *not* have submodules.
- Each team has it's own GitHub organization to limit write-access.
- Modules/Frameworks refer to their dependency projects by ../
- Suggested that the only branch ever in use is Master.
- Only version tags or 'master' should be used for checkout purposes.
- Feature or Fix branches may be used, but must be deleted after a PR or Version tag is created for it.
- Forks are only used in the case of Modifying a dependency you don't own.


<a name="versioning"></a>
## Versioning

Given this example:

```
v2.1.1
```
We follow semver fairly closely.  This is release 2, with 1 additional feature added, and 1 bug fix.  One place where we deviate from semver is in the definition of the decimal places in the string.

BREAKING.FEATURE.FIX is more descriptive of what we do than Semver's MAJOR.MINOR.PATCH.

Try to avoid things like v2.1.1-rc3, and the like.  If there were 3 release candidates, it's likely fixes were made, so increment the FIX number instead.  In the future tools will be available such that you can specify that any version greater than 2.0 is supported, ie: "<= v2.0.0", which will eliminate the need to fixup version mismatches.


<a name="add-dep-to-app"></a>
## Adding a dependency to an application

Link your submodule directly to the org repo that owns the respository.  They will be responsible for their own releases.  For instance, imagine an iPhone app called "MyApp", which is in an org called "MyAppOrg".  Now imagine a feature owned by a different group called "AFeature" in an org called "FeatureTeam".

An example would be:

```bash
./MyApp $> git submodule add git@github.com:featureteam/afeature.git ./Modules/AFeature
./MyApp $> git sudmodule update --init
./MyApp $> cd Modules
./MyApp/AFeature $> git checkout "v1.0.0"
```

Notice how this example did *not* fork AFeature.  It instead links directly to AFeature at a specific version.


## Adding a dependency to your Application's Xcode project<a name="use-dep-in-app"></a>

Simply drag Modules/AFeature/AFeature.xcodeproj to your MyApp project.  Once you've done that, then add the produced AFeature.framework to the list of Embedded Binaries.  See the example below:

![Example](https://github.com/modulo-dm/workflow/raw/master/adddeptoapp.gif "Example")

Adding it to Embedded Binaries will also include it in the list of Linked Frameworks.  In cases where your dependency has things that it depends on, that your application does not, it may be necessary to add those to the list of Embedded Binaries as well.

Note: This process is the same whether you own the dependency or not.


<a name="add-dep-to-module"></a>
## Adding a dependency to a module/framework

Do *not* add your dependency as a submodule.  Instead go one directory up, "../", and clone it there.

In this example, there is a module being developed called "MyModule", and it requires "MyLibrary" to build.

```bash
./MyModule $> cd ../
./ $> git clone git@github.com:libraryteam/mylibrary.git MyLibrary
./ $> cd MyLibrary
./MyLibrary $> git checkout "v1.0.0"
```

The reason we clone to "../" is to allow all dependencies to live side by side.  This makes working on an Application or a Module/Framework reference any dependencies in the exact same way.  Regardless of whether you are working on an Application or Framework, dependencies are in the same place as each other now.


<a name="use-dep-in-module"></a>
## Adding a dependency to your Module's Xcode project.

Simply drag ../MyLibrary/MyLibrary.xcodeproj to your MyModule project.  Once you've done that, then add the produced MyLibrary.framework to the list of Linked Libraries.  See the example below:

![Example2](https://github.com/modulo-dm/workflow/raw/master/adddeptomodule.gif "Example2")

Note: This process is the same whether you own the dependency or not.


<a name="modify-owned-dep"></a>
## Modifying a dependency that you own

In this example, imagine we're working on a module called MyFeature, and we need to make a change to MyLibrary to facilitate that.

At this point in time, we should currently be on a specific version tag of MyLibrary.  Go into the MyLibrary directory ("../" if you're building a module, "modules/" if you're working on an Application) and switch to 'master'.  Do a Pull to make sure you have the latest code.

```bash
./MyFeature $> cd ../MyLibrary
./MyLibrary $> git checkout master
./MyLibrary $> git pull origin
./MyLibrary $> git checkout -b "My Fix"
```

You'll now be able to make the modifications that are needed in a newly created branch called 'My Fix'.  You'll be able to build and test these changes in your app.  When you've finished, you should now do a PR from your 'My Fix' branch back to 'master' and get a peer to review it.  

Once the PR has been accepted, the reviewer should delete the branch.


<a name="modify-unowned-dep"></a>
## Modifying a dependency that you don't own

This is the only case where a fork is acceptable.

In this example, imagine we're working on a module called MyFeature, and we need to make a change to MyLibrary to facilitate that, and it's owned by another team.

First thing we'll need to do is Fork it.  This can be done on github, as shown here:

![Example3](https://github.com/modulo-dm/workflow/raw/master/wheretofork.png "Example3")

Next, you'll want to make sure you check out the tag of the release you were on, and start a new branch from it.

```bash
./MyFeature $> cd ../
./ $> git clone git@github.com:libraryteam/mylibrary.git MyLibrary
./ $> cd MyLibrary
./MyLibrary $> git checkout "v1.0.0"
./MyLibrary $> git checkout -b "My Fix"
```

You'll now be able to make the modifications that are needed in a newly created branch called 'My Fix'.  You'll be able to build and test these changes in your app.  When you've finished, you should now do a PR from your 'My Fix' branch back to the original org's repository.  When accepted, either delete your fork, or at the very least pull from the original so that your master and list of tags matches theirs.


<a name="making-releases"></a>
## Making a release

Go to your project on Github.  Once there perform the steps below:

![Release1](https://github.com/modulo-dm/workflow/raw/master/release1.png "Release1")
...

![Release2](https://github.com/modulo-dm/workflow/raw/master/release2.png "Release2")

Lastly, fill out the form appropriately.  If your change hasn't been PR'd be sure to check the "Pre-release" box.

Remember, use Semver formatting when you specify your tag name, ie: v1.2.3.  Once you've done that, select the branch you made your changes on from the dropdown to the right of where you entered the tag name.

![Release3](https://github.com/modulo-dm/workflow/raw/master/release3.png "Release3")

### Writing Release Notes

Write a descriptive list of all breaking changes, new features, deprecations, and minor fixes. If you are disciplined about writing descriptive commit messages and isolating changes in separate commits you should be able to use the output from a `git log` command as a good starting point for the list of changes.

Use `git log` to log a diff between your changes and the upstream branch:

```
git log --no-merges v2.0.01..master --pretty=format:'- %s'
```

An example of writing good release notes can be found here: https://github.com/Electrode-iOS/ELWebService/releases/tag/v3.0.0

You should strive to be as thorough as the example.  It's very helpful to those who depend on you.


<a name="updating-dep"></a>
## How to update a dependency

If you're working on an Application:

```bash
./MyApp $> cd modules/MyFeature
./MyApp/modules/MyFeature $> git fetch --all
./MyApp/modules/MyFeature $> git checkout "v1.0.1"
```

If you're working on a Module/Framework:

```bash
./MyFeature $> cd ../MyLibrary
./MyLibrary $> git fetch --all
./MyLibrary $> git checkout "v1.0.2"
```

It's possible that you may need to go back to your app and fix any changes in the usage of the dependency you just updated.

It is very important that you point your dependency to a specific version tag as much as possible.  A notable exceptions to this is modifying a dependency that is changing rapidly during development.  In that case, it may make more sense to point to 'master' temporarily while this work happens.


<a name="q-and-a"></a>
# Q & A

## What if Master is at v2.0.0, and I need to make a fix to v1.0.0?

Follow the appropriate steps above to the point of making a PR.  At this point, you'll want to make a new release that includes your bug fix.  Since your fix is for v1.0.0, your new release tag will be v1.0.1 (refer to versioning above) and point to the SHA your branch is using.  It's best to tag your release at this point and check the "Pre-Release" box, since it hasn't been accepted into the master yet.  Once tagged and PR'd, your fix branch can be deleted.  You'll have to manage getting your fix PR'd into master seperately as things may have changed enough that it doesn't merge cleanly.

## When should I make a pre-release?

If you need to share your changes amonst your peers and the changes haven't been PR'd or accepted into the mainline yet, a pre-release is very useful.  It allows others to point to it in the meantime while you're waiting on acceptance.

## Should I change how I submit PRs?

Probably.  Assume you're PR'ing to master, provide as much information to the reviewer as possible.  It may even be a repeat of your pre-release tag information.  Include information about any fixes that were performed, breaking changes, etc.  If you made a pre-release, provide the reviewer with the tag name you created.

## Should I change how I review PRs?

Yes.  You should review the pre-release tag if one was given by the submitter, if it is insufficient, make note in the PR request and send it back to the submitter.  If everything looks good, be sure that you check the box to delete the branch as well.  This will keep branches to a minimum since we're focusing development on master.

## Will it be possible for two things have a common dependency on different versions?

No.  If two modules share a common dependency, they must both use the same version.  This prevents duplicated code, but does involve more work.  No one wants a situation where the same library is included 5 times with different versions.  It will be very important that modules are kept up to date.
