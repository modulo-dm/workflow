# workflow
Describes the suggested workflow.

## Ground rules

- Only applications should have submodules.
- Modules/Frameworks should *not* have submodules.
- Each team has it's own organization to limit write-access.
- Modules/Frameworks refer to their dependency projects by ../
- Suggested that the only branch ever in use is Master.
- Only version tags or 'master' should be used for checkout purposes.
- Forks are only used in the case of Modifying a dependency you don't own.

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

## Adding a dependency to your Application's Xcode project

Simply drag Modules/AFeature/AFeature.xcodeproj to your MyApp project.  Once you've done that, then add the produced AFeature.framework to the list of Embedded Binaries.  See the example below:

![Example](https://github.com/modulo-dm/workflow/raw/master/adddeptoapp.gif "Example")

Adding it to Embedded Binaries will also include it in the list of Linked Frameworks.

## Adding a dependency to a module/framework

Do *not* add your dependency as a submodule.  Instead go one directory up, "../", and clone it there.

In this example, there is a module being developed called "MyModule", and it requires "MyLibrary" to build.

```bash
./MyModule $> cd ../
./ $> git clone git@github.com:libraryteam/mylibrary.git MyLibrary
./ $> cd MyLibrary
./MyLibrary $> git checkout "v1.0.0"
```

The reason we clone to "../" is to allow all dependencies to live side by side.  This makes working on an Application or a Module/Framework reference any dependencies in the exact same way.  Regardless of whether you are working on an Application for Framework, dependencies are in the same place as each other now.

## Modifying a dependency that you own

## Modifying a dependency that you don't own

## Making a release

## When to make a pre-release

## How to update a dependency
