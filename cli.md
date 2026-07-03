## Invoking the Game's Command Line Interface

*Fields of Mistria* has robust logging and a complex command line interface (CLI). This document serves as a guide on how to view those logs. This is the **ideal** way to test mods for the game, as you'll get stacktraces, debug messages, and warnings, beyond what is available in the crash window.

To do that, navigate to *Fields of Mistria*'s installation folder by right clicking the game in your library, and then navigating to "Manage" and then "Browse Local Files".

![An image showing right-clicking the Steam game *Fields of Mistria* and clicking "Browse Local Files"](browse-local-files.png)

Then, in this folder, open a Terminal to invoke the game manually (in Windows 11 you can often right click "Open Terminal here"). Otherwise, you can copy the path from the File Explorer and manually move a terminal there using `cd`.

Here is an example in Ubuntu and Windows 11 of opening a terminal in this location:

![An image showing right-clicking in the system file explorer and pressing "Open in Terminal"](open-terminal-kde.png)

Then you can invoke the game depending on platform and terminal:

### Invoking Mistria in Powershell with Windows

Powershell, and Windows in general, make getting the command line output very difficult. Here is the best way to invoke Fields of Mistria
and get output:

```pwsh
& ./FieldsOfMistria.exe 2>&1 --help | Write-Host
```

Note the `&` at the start: this is the [call operator](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_operators?view=powershell-7.6#call-operator-) which allows you to invoke the executable directly in the folder. `2>&1` and `| Write-Host` are both used to force the Window executable to output to a terminal.
### Invoking Mistria on Unix

On Unix platforms, it is far simpler:

```sh
./FieldsOfMistria --help;
```

In either case, you'll be greeted by Mistria's help options.

## Mistria's CLI Options
Here are the following cli options for Fields of Mistria:

**Usage on pwsh:** `./FieldsOfMistria.exe 2>&1 [OPTIONS] [COMMAND] | Write-Host`
**Usage on unix:** `./FieldsOfMistria [OPTIONS] [COMMAND]`

**The most important command** is `./FieldsOfMistria --help` which will list all of this information out directly.

###### **Options:**
You can give no command and still set these options to run the game normally with other options. For example, to run the game on Unix at 240 fps, `./FieldsOfMistria --fps=240`.

You can also just add these options into Steam's command line options for the game, though this will change how the game runs *every time* going forwards, which we don't recommend:

![An image showing right-clicking the Steam game *Fields of Mistria* and clicking "Properties"](go-to-properties.png)

![An image showing command line options being added to the "Launch Options" tab under "General"](steam-cli-options.png)

* `--all-unlocks` — Starts new save files with all content available by default

  Default value: `false`
* `--story-events` — Enables cutscene triggers for story events starting with the prologue and continuing through the rest of the story's events

  Default value: `true`
* `--debug-tools` — Enables tools like `Bugger`, `obj_cosmic_debug`, and other debugging based code paths

  Default value: `false`
* `--debug-assertions` — Enables stricter error checking in various places around the code base

  Default value: `false`

* `--auto-start <AUTO_START>` — Skips the main menu on boot. `continue` loads the most recent save, `new` starts a new game

  Default value: `off`

  Possible values:
  - `off`:
    Boot to the main menu normally
  - `continue`:
    Skip the main menu and load the most recent save
  - `new`:
    Skip the main menu and start a new game

* `--fps <FPS>` — The target fps for the game. The game is only intended to be played at 60

  Default value: `60`

###### **Subcommands:**

* `test` — Runs the test suite
* `sha` — Returns the Git Sha which was the most current when the game was built, if we happen to know it. If we don't, nothing is written and an error is returned

## `./FieldsOfMistria test`

Runs the test suite

**Usage:** `./FieldsOfMistria test [OPTIONS] [TESTS_TO_RUN]...`

###### **Arguments:**

* `<TESTS_TO_RUN>` — The names of the tests to run. Besides the names of tests, this command also accepts:

   - `standard` for the standard suite of tests.
   - `all` for every test.

   There are also some important longer tests which we run often:
   - `general_cutscenes` to run miscellaneous cutscenes. Note: this test generally requires `all-unlocks` to be true.
   - `shooting_star` to run all shooting star event scenes.
   - `main_story` to run the main town story sections.
   - `dragon_story` to run the dragon (mines) story sections.
   - `festivals` to run all the festivals except shooting star's variants.
   - `dateable_progression` to run every heart event for the NPC set in `npc_test_target`. When running `dateable_progression`, you *must* also set an `npc_test_target`.

  Default value: `standard`

###### **Options:**

* `--npc-test-target <NPC_TEST_TARGET>` — The target npc to write, used for the test `dateable_progression`

  Possible values: `adeline`, `balor`, `caldarus`, `celine`, `eiland`, `hayden`, `juniper`, `march`, `reina`, `ryis`, `seridia`, `valen`

* `--seasons <SEASONS>` — What seasons to run the given tests in

  Default value: `spring`

  Possible values: `spring`, `summer`, `fall`, `winter`

## `./FieldsOfMistria sha`

Returns the Git Sha which was the most current when the game was built, if we happen to know it. If we don't, nothing is written and an error is returned

**Usage:** `./FieldsOfMistria sha`
