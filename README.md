# hyproled

A Hyprland shader utility to help prevent OLED burn-in by disabling every other pixel.

![Demo usage switching on and off](https://github.com/mklan/hyproled/blob/main/demo.gif)

## Usage

```
Usage: hyproled [OPTION]... [off]

Options:
  -a x:y:w:h  Set the active area (useful for bars). [Default: entire screen]
  -i          Invert the area (useful for window or popup focus effects).
  -s          Shift the lit pixels (run periodically to prevent burn-in).
  -m ID       OLED monitor ID (see `hyprctl monitors`). [Default: 0]
  -c color    Set the color [Format: 0.0,0.0,0.0,1.0]
  -h          Show this help message.

Arguments:
  off         Disable hyproled
  last        Show the arguments used in the last execution
```

## Installation

1. Install via AUR: [hyproled-git](https://aur.archlinux.org/packages/hyproled-git)

   ```bash
   yay -S hyproled-git
   ```

or

2. Manually copy the binary:

   ```bash
   sudo cp hyproled /usr/bin/hyproled
   ```

## Getting Started

Running `hyproled` overlays the entire screen with a 1px checkerboard pattern.

Since this effect reduces overall brightness, you may want to apply the shader only to static areas, such as bars.

```bash
hyproled -s -a 0:0:2880:50
```

Each time you run this command, it shifts the disabled pixels. To further prevent burn-in, you can schedule this to run periodically, for example, every hour using cron:

```bash
 0 * * * * hyprctl --instance 0 dispatch -- exec "hyproled -s -a 0:0:2880:50"
```

See the [examples](examples) directory for a more advanced burn-in prevention script.

## Disabling in Fullscreen Applications

If you limit hyproled to a specific area, you may want to disable the shader when an application enters fullscreen mode.

```bash
#!/usr/bin/env bash

handle() {
  case $1 in
    'fullscreen>>1') hyproled off ;;
    'fullscreen>>0') hyproled -a 0:0:2880:50 ;;
  esac
}

socat -U - UNIX-CONNECT:$XDG_RUNTIME_DIR/hypr/$HYPRLAND_INSTANCE_SIGNATURE/.socket2.sock | while read -r line; do handle "$line"; done
```

## Roadmap

- Support for multiple areas
- Support for multiple monitors
- Ability to combine with a custom shader (Hyprland allows only one shader at a time)
