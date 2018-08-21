# lib-common

`lib-common` is a repository of common software used in multiple subsystems of the satellite. It has its own GitHub repository for development (`lib-common`), and is embedded in other subsystem repositories as a Git submodule so it can be used in those repositories.

A submodule is code from one repository that is embedded in another repository to be used. We use this to embed code from the `lib-common` repository in other repositories such as `pay`.


## Update lib-common

To update the `lib-common` submodule to the latest code from the `lib-common` repository, run this command from your other repository's root:

`$ git submodule update --remote`

You must then recompile `lib-common`:

```
$ cd lib-common
$ make
$ cd ..
```


# Switch lib-common to a Different Branch

Normally, when you run `$ git submodule update --remote`, it fetches the latest version of `lib-common` from `master`. You can get Git to track a different branch of `lib-common` by modifying the `.gitmodules` file. If you open it, you will see something like this:

```
[submodule "lib-common"]
	path = lib-common
	url = https://github.com/HeronMkII/lib-common.git
```

Add the following line to track a different branch:

`branch = <branch name>`

For example, if you want to use the branch `flash-dev`, your `.gitmodules` file will look like this:

```
[submodule "lib-common"]
	path = lib-common
	url = https://github.com/HeronMkII/lib-common.git
	branch = flash-dev
```
