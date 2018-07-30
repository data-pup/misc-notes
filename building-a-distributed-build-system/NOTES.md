# Building a Distributed Build System at Google Scale • Aysylu Greenberg

### First, some general points of scale:

*  30,000 developers, with ~45k daily commits made.

*  Roughly 2-billion LOC.

*  ~5 milling builds and tests in a single day, generating petabytes of output
artifacts. This is all done within a single repository!

### Why a single repository?

*  Linear revision history
*  Everything is cross-referenced
*  Components for library releases
  *  This is similar to git subtree's, to differentiate WIP versions
*  Repository of artifacts vs. building from source:
  *  Predictable, repeatable builds from source
  *  Optimizations to avoid compiling same artifacts
  *  Decouple each team's processes

### Building a Distributed Build System

Not every organization needs a distributed build system. It solves a specific
problem! What does the progression to reach that point look like?

First, a single machine might build. What does building mean?
*  For some project, fetch its dependencies
*  Build project with the dependencies
*  Download build artifacts

For a testing scenario, the steps look near identical, but the final step will
consist of running tests and collecting their results.

Next, a team might set up a server for building their project. They might use
some tools for CI for example. As more teams must build this project for their
own uses, they must spend more time tending to their build server.

At this point, a team might set up a distributed build system for these teams,
so that they do not need to take care of these details themselves.

Google's build tool is called `BuildRabbit`. This adds a layer over Bazel! It
sits in the middle of the build and test systems, between various teams, CI
systems, and integration testing infrastructure on side, and the source system
on the other.

### Zero Downtime

The team was able to migrate without any downtime! How?

Back-ends had to be migrated first. This way, the new back-ends could prove
that they were able to withstand the loads required.

Throw-aray code is needed to prove identical functionality.

Launch friendly clients had to be created first. Having both the user and the
infrastructure team using shared vocabulary and sharing concerns helped reduce
communication overhead. Further, the different components in the new system
would need to have decoupled releases, so that they could release at different
times safely.

This last point is worth noting, each component would need to assume that they
could not rely on any of the other new components.

Practice launches! Despite having thorough tests, a practice deployment into
the QA environment failed initially. Having a solid rollback plan was also
very important.

