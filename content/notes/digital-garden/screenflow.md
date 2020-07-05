# Screenflow

## Installation

You normally should be able to install
[Screenflow](http://www.telestream.net/screenflow/overview.htm) with Homebrew:

```
brew cask install screenflow
```

However, this
[currently fails](https://github.com/Homebrew/homebrew-cask/issues/84783). In
the error message, you can see the actual download location and manually
download and install Screenflow until this is fixed.

## Configuration

Screenflow needs the following permissions in System preferences => Security &
Privacy:

- Camera
- Microphone
- Screen Recording
- Accessibility (optional for keystroke recording)

## Microphone calibration

In system preferences => Sound => Input, choose your microphone and put input
volume to 50%. Move volume up while talking until input level reaches
consistently 75%.

If you maxed the input volume at the OS level but it is not high enough, you can
still bump it up a in Screenflow's settings behind the audio cog wheel.

## Keyboard shortcuts

| Shortcut              | Description                     |
| :-------------------- | :------------------------------ |
| Command + left arrow  | Move scrubber to the beginning. |
| Command + right arrow | Move scrubber to the end.       |
| t                     | Trim and select incoming media. |
| shift + t             | Trim and select outgoing media. |
| Command + z           | Undo.                           |