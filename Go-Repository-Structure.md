We used to have a single repository that housed all of our Go projects. We have since decided to move to a single repository for each project, with one repository for all of our shared libraries. We [vendor](https://github.com/thumbtack/wiki/wiki/Golang-Libraries-and-Vendoring) all libraries, including shared libraries that we write.

## Each Project in its own Repository

**Pros**

* Open-sourcing (libraries) is easier
* Deployment is straightforward
* Could remove the debian packaging system and deploy repository as-is
* Could use `eb deploy` to deploy to test environments
* Works well with vendoring
* Scales with more developers and more projects
* Self-contained & logically independent
* Travis integration is straightforward
* Updating dependencies won’t break code in other projects

**Cons**

* Hard to keep track of versions and dependencies with shared libraries
* Urgent bug fixes and backward compatible improvements to shared libraries would need to be handled independently for every project

## Alternative: Single Repository for Everything

**Pros**

* Makes sure all projects use up-to-date versions of shared libraries
* Go compilation is fast, so not a concern
* Vendored libraries are all in one place, no dup
* You know immediately if you break something when changing shared code

**Cons**

* Lots of files with same name hard to find if you don’t have good dev env setup
* Have to update shared libraries immediately vs. deciding on the schedule for yourself
* Might have to constantly rebase after other developers changes
* A whole bunch of commits you don’t care about
* Projects are not logically dependent on each other, why version them together?
* Deployment would be harder to implement (would need the special directory structure with different versions of Dockerfiles)
* Current Python “powerball” (aka cesspool) repo is a mess

## Discussion
For more context around the decision, see the relevant section in the [Go Conventions RFC](https://docs.google.com/document/d/1hA903WGGMwjcho-spGTtIbrxBZ_x5xfgNRCzervrmNM/edit#heading=h.xg210a49z564).