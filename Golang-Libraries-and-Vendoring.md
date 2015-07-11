# Vendoring

We use `godep` to "vendor", both for third party libraries, and for libraries that we write (in the `gott` repo). This basically means that you make a copy of the libraries and save them into your own repo. See github page [here](https://github.com/tools/godep).

tl;dr:

* Installing `godep`:
  * `go get github.com/tools/godep`
* Vendoring everything:
  * Run `go get -u` on every package that you need, eg. `go get -u github.com/thumbtack/gott/db`
  * Change your code to import the new packages.
  * `godep save -r ./...`
* Updating packages:
  * `godep update github.com/...` to update pkgs from github
  * `godep update [full import path]` to update a specific pkg
* Checking in the files:
  * `git add --all Godeps`

NB: We have decided to use the `-r` option to `godep save`, which rewrites all of your imports. See below for more information. This sometimes introduces unwanted extra edits, which may need to be cleaned up.

In order to use the vendored libraries, you will need to prepend all of your `go` commands with `godep`. The make file does this for you already. But for the sake of example, to build your project, you would need to run `godep go build`.

If you're no longer using a dependency, one way to remove it is to remove the `Godeps` folder, and rerun `godep save ./...`.

# TT shared libraries (gott)

We don't vendor deps in our common libraries in gott to avoid bringing in different versions of the same 3rd party libs into the services. The services control what versions of 3rd party libs they want.

# 3rd party libraries we use

* [aws-sdk-go](https://github.com/awslabs/aws-sdk-go) (Agonas uses goamz. Let's use aws-sdk-go moving forward, since it looks like the AMZ is supporting it.)
* [Ooyala's dogstatsd client](https://github.com/ooyala/go-dogstatsd/). There is an [official version of the client](https://github.com/DataDog/datadog-go) provided by Datadog, but it is not listed on their [libraries page](http://docs.datadoghq.com/libraries/), so we're assuming it's not production ready yet. More research could be done into which library would be best to use.

# Import Path Rewriting

We have decided to use import path rewriting, primarily so that the normal Go toolchain works as expected, and development is as easy as possible for people new to writing Go services.

Reasons we didn't rewrite imports for a while:
* Zim had a lot of trouble dealing with the rewritten import paths when changing `gott/shared` to just be `gott`
    * This scenario will be extremely rare
* It's much easier to read
* Our Makefile abstracts away the need to prepend `godep` on `go build`
* Static analysis tools (`go vet` and `golint`) are unaffected by this
* The people at Docker don't rewrite their import paths
    * Source: Peter's roommate. We don't have the specifics of why they decided to do this.

The downsides of that decision:
* The normal go toolchain doesn't work for building
* IDE integration becomes trickier
* Packages are not `go get`able
    * Not really a concern for us now, but could be in the future

# Refactoring in vendored libaries
When a package in one of your vendored libraries moves locations, it will probably break the next time you run `godep save`. To fix this, you'll need to take some steps to point at the new package locations. For example, this happened when we moved the db package in gott from `gott/shared/db` to `gott/db`.

1. Grep for all references to the old package, eg. `git grep 'gott/shared/db'`. Change all of the import statements to reference the new package location, in this case 'gott/db'.
2. Run `godep restore ./...`. This is like the opposite of godep save.
3. Run `godep save -r ./...` again. This should bring in the new package. Verify that Godeps.json has the new package.
4. You may need to manually remove the actual code from the old package in the Godeps/ folder.

# FAQs / Common Errors

## godep: github.com/somepackage/blah: revision is foo, want bar
This probably means some previously existing dependency that you had imported from "somepackage" needs to be updated to a newer version. Run `godep update github.com/somepackage/...` before running godep save. Here's a [thread](https://github.com/tools/godep/issues/85) on the topic.
