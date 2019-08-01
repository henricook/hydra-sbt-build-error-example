# Example project for a bug i'm seeing when using Hydra 2.1.9

## Prerequisities
 - Scala 2.13 (also happens for me on 2.12)
 - SBT 1.2.8
 - Hydra configured as an SBT **global** plugin

## Steps to reproduce

Open `main-project`, run `sbt compile`

Assuming it's committed correctly `mirror-project` should be a project with its own `target` folder but _everything else_ is soft linked to main project. If the links aren't intact run these commands from `mirror-project`:

```
# ln -s ../main-project/build.sbt build.sbt
# ln -s ../main-project/project project  
# ln -s ../main-project/src src
```

run `sbt compile` in the mirror project and you'll get this error:

```
[error] .../src/main/scala/example/model/package.scala:3:16: package is already defined as package object model
[error] package object model {}
[error]                ^
[error] one error found
[error] (Compile / compileIncremental) Compilation failed (CompilerInterface)
[error] Total time: 3 s, completed 01-Aug-2019 12:03:57
```

This seems to be colliding with `main-project` - even though we're in `mirror-project` with a different target directory!

## Why do i do this craziness?

I run two sbt sessions on a project so that i can be in `sbt run` and in another sbt window be running `sbt acceptance:test` (which requires a running service). If I try to use two sbt sessions from the same project, they often clash if they compile together accidentally, and it gets annoying - so this symlinking/clone was my solution.

This works without Hydra and i'd be interested in the cause / it'd be great for my workflow if i could keep working this way.