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

Link your sumodule directly to the org repo that owns the respository.  They will be responsible for their own releases.  For instance, imagine an iPhone app called "MyApp", which is in an org called "MyAppOrg".  Now imagine a feature owned by a different group called "AFeature" in an org called "FeatureTeam".

An example would be:

```bash
MyApp $> git submodule add git@github.com:featureteam/afeature.git .\modules
MyApp $> git sudmodule update --init
MyApp $> cd modules
MyApp/AFeature $> git checkout "v1.0.0"
```

Notice how this person did *not* fork AFeature.  They instead linked directly to it at a specific version.

## Adding a dependency to a module/framework

## Modifying a dependency that you own

## Modifying a dependency that you don't own

## Making a release

## When to make a pre-release

## How to update a dependency
