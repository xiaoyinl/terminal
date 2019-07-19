---
author: Dustin Howett @DHowett-MSFT
created on: 2019-07-19
last updated: 2019-07-19
issue id: "#xxxx"
---

# Improvements to CloseOnExit

## Abstract

This spec describes an improvement to the `closeOnExit` profile feature that will offer greater flexibility and allow us to provide saner defaults.

## Inspiration

Other terminal emulators like ConEmu have a similar feature.

## Solution Design

1. The existing `closeOnExit` profile key will become an enumerated string key with the following values/behaviours:
   * `always` - a tab or pane hosting this profile will always be closed when the launched connection terminates.
   * `graceful` - a tab or pane hosting this profile will only be closed **if** the launched connection closes with an exit code != 0.
   * `never` - a tab or pane hosting this profile will not automatically close.
2. The `ITerminalConnection` interface's `Disconnected` event will grow a new parameter, `ConnectionClosedEventArgs` (pending name), which will bear an exit code.
3. As `TerminalApp` is responsible for producing connection instances, _it_ will subscribe to this event.

The new default value for `closeOnExit` will be `graceful`.

## UI/UX Design

As above.

## Capabilities

### Accessibility

This will give users of all technologies a way to know when their shell has failed to launch or has exited with an unexpected status code.

### Security

There will be no impact to security.

### Reliability

Windows Terminal will no longer immediately terminate on startup if the user's shell doesn't exist.

### Compatibility

There is an existing `closeOnExit` _boolean_ key that a user may have configured in profiles.json. The boolean values should map as follows:

* `true` -> `graceful`
* `false` -> `never`

This will make for a clean transition to Windows Terminal's sane new defaults.

### Performance, Power, and Efficiency

## Potential Issues

There will be no impact to Performance, Power or Efficiency.

## Future considerations

* Eventually, we may want to implement a feature like "only close on graceful exit if the shell was running for more than X seconds". This puts us in a better position to do that, as we can detect graceful and clumsy exits more readily.
