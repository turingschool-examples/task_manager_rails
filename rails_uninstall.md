## So You Need a New Rails version?

In order to install a new (or different) version of Rails, you will first need to uninstall rails *entirely* with the following commands:

```
$ gem uninstall rails
```
If prompted for a specific version, select the one(s) you would like to uninstall.

```
$ gem uninstall railties
```

You may be asked if you are _sure_ about uninstalling specific gems that rails is dependent on; you should 'yes' through each of these prompts.

Once you have uninstalled rails and its railties, you can then install the appropriate version with:

```
$ gem install rails -v 5.1.7
```

And run `$ rails -v` to confirm you now have the correct version!
