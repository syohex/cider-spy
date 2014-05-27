THIS PROJECT IS UNSTABLE / IN DEVELOPMENT
RIGHT NOW IT'S GROSSLY UNSTABLE - COME BACK SOON!

# CIDER Spy

Spy on CIDER nREPL sessions and help developers and teams share information, send code snippets to each other etc.

# Installation

## Prerequisites

You need the [`CIDER-SPY-NREPL`](https://github.com/jonpither/cider-spy-nrepl) middleware. See installation instructions there. `CIDER-SPY` also depends on [`CIDER`](https://github.com/clojure-emacs/cider).

If you want the developer interactivity behavours, then you need a run a `CIDER-SPY-HUB`. See the middleware documentation for how to set one up. It's trivial enough.

## Configuration

You can certainly use `CIDER-SPY` without configuring it any further
to get summary information about your own REPL session. But configuration
is needed if you want to connect to a `CIDER-SPY-HUB` to interact with your
colleagues.

### Basic configuration

* Set `cider-spy-hub-endpoint` to where the `CIDER-SPY-HUB` is running.

```el
(setq cider-spy-hub-endpoint '("localhost" 7771)
```

* Give yourself an alias on the hub.

```el
(setq cider-spy-hub-alias "my-alias")
```

# Keyboard Shortcuts

### cider-spy-mode

Keyboard shortcut                    | Description
-------------------------------------|-------------------------------
<kbd>g</kbd>| Refresh the `*cider-spy*` buffer.
<kbd>r</kbd>| Reset the tracking data underpinning the `*cider-spy*` buffer.
<kbd>n</kbd>| Goto to next section.
<kbd>p</kbd>| Goto to previous section.
<kbd>a</kbd>| Set `CIDER-SPY-HUB` alias.
<kbd>s</kbd>| Send message to another dev (when cursor is on a dev).
<kbd>RETURN</kbd>| Visit section.
<kbd>TAB</kbd>| Toggle section visibility.

# Background

This originally started life as a tool to help users learn about their own REPL behavour, and to function as an uber smart project navigation tool.

Since then it's morphed into being a tool to help developers share information. I.e. to ping code to each other and see where colleagues are.

## Feature ideas

* Functions run most often outside of immediate namespace
* Functions run that fail most often
* Time Fns: https://github.com/clojure-emacs/cider/issues/477. Then can show "longest running fns".
* REPL Summary: I spent x amount of time in this namespace today

## Refactor ideas

* Right now summary is refreshed all the time - I should make a better process of firing off deltas (i.e. registrations). Perhaps I should start by having a dedicated 'flash message' section of the spy buffer.
* Refactor: Split out an interaction file.
* Test: Do a ping
* Document credits in official way. Magnars because I used a lot of `magit` patterns and `clj-refactor` build setup. The `CIDER` team.

## Config Rumination

(This rumination is the start of a section in the docs that explains the thought process behind auto-subscribing everyone to participate in the `CIDER-SPY-HUB`. People will be rightfully worried about auto connecting and being spied on.)

The config `cider-spy-hub-endpoint` is a project based setting, not a personalised one. Therefore it shouldn't live in some personal Emacs config, it should live in the project root of the Clojure project.

This has the added benefit the `NREPL` middleware to `CIDER-SPY-HUB` communication can work without a dependency on the `CIDER-SPY` Emacs package feeding it config. When devs install `CIDER-SPY` they can then can see where other devs are, without the other devs having to install `CIDER-SPY`. This lowers barrier to entry.

If this is too intrusive, i.e. devs resent being spied on without their knowledge, then there are two answers: 1) The use of `CIDER-SPY-HUB` is a centralised project setting and should be team decision to adopt, and 2) we could implement a feature where the individual user can opt out*.

Where to put the centralised `cider-spy-hub-endpoint` config? I don't think it's right to put in `project.clj` and TBH I'm not sure how either - this is build time config. Perhaps then just stick a `.cider-spy-root` file in the project root. This looks to be an established pattern, i.e. `.projectile`, `.gitignore` etc.

Another config option is to use `ENVIRON` to allow users another chance at overriding the central project settings in `.cider-spy-root`. A further option is to use `ENVIRON` full stop and cut out the middle file. The downside of this is that whereas `.cider-spy-root` can be version controlled as a centralised project setting, the `.lein` file generated by `ENVIRON` shouldn't be as it typically contains personalised settings.

* Once config storage has been sorted, we could give the option to `cider-spy-hub-connect-on-demand`. If true the nrepl-server instance only connects to hub when user initiates it through `CIDER-SPY`.

## Multiple Sessions Rumination

There are interesting questions at the CIDER-SPY level. The *hub* buffer should be per NREPL server instance, as should *cider-spy*, even though the *cider-spys* will contain duplicate information. Maybe in time the *cider* spy buffer will sit above all the different NREPL connections. For now though, it's a piece of work to separate them out, i.e. use the cider-spy designation and apply the same pattern. Killing a NREPL server instance should also kill the spy instance (definitely if spy buffers become 1-2-1 with CIDER buffers). In some ways it's less work to do to make CIDER-SPY completely span NREPL... I at least could consider this.

If I'm following the mantra of getting stuff working asap, then perhaps ignoring all this and considering ayncing on alias is the way to go (or some simple ID). Perhaps I can divorce the two concerns of SPY working with multple NREPL sessions. In the first release, just say simply it can't. What would happen though? The buffers would compete, overwriting each others stuff... not a big deal TBH.

# License

Copyright © 2014 Jon Pither

Distributed under the GNU General Public License, version 3
