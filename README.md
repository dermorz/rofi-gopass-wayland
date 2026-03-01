# rofi-gopass-wayland

A [rofi-wayland](https://github.com/lbonn/rofi) frontend for [gopass](https://github.com/gopasspw/gopass). This is a fork of rofi-pass-wayland adapted to work with gopass.

Main goal - provide frontend for gopass, while keeping it a small, readable
single-file script.

## Features

- Create / Read / Update / Delete / move / copy password store entries
- Generate new passwords for entries
- Copy / type individual fields (or autotype based on `autotype` field) of selected entry
- Support for different password store roots (e.g. personal & work)
- Open URLs of entries with hotkey
- Generate and display QR code directly in rofi ([qrencode](https://fukuchi.org/works/qrencode/) needed).
- Generate and autotype in OTPs
- Extensive configuration (see [config.example](./config.example))
- Clean codebase, linted with [shellcheck](https://github.com/koalaman/shellcheck) and formatted with [shfmt](https://github.com/mvdan/sh)

Use default binding `Alt+h` to see available bindings. And pass `--help | -h` flag to
this script to see all available flags.

## Dependencies

- [gopass](https://github.com/gopasspw/gopass)
- [rofi-wayland](https://github.com/lbonn/rofi)
- [wtype](https://github.com/atx/wtype)
- [wl-clipboard](https://github.com/bugaevc/wl-clipboard)
- [qrencode](https://fukuchi.org/works/qrencode/) (optional: for generating and displaying gopass qrcode)

## Installation

Copy `rofi-gopass` script to dir in your `$PATH`. Script has all needed defaults, but if you want to customise further you can pass your config to the script with one of the following ways:

- `$HOME/.config/rofi-gopass/config`
- `ROFI_GOPASS_CONFIG="/path/to/config"` env var.

See example configuration in [config.example](./config.example) file.

## Autotyping

Default autotype value is `user :tab pass`. In case you don't have `user:`
field in your password file it will take value of `default_user` from your
config (defaults to output of command `whoami`). Or you can set
`default_user` to `:basename` and autotype will always type basename of
password file as username. (i.e. for `foo/bar/site.com/jack` it will type
`jack` as username)

Example of password store file with autotyping of more than one field, using the
`autotype` entry:

```sh
# mail/gmail/john@gmail.com
de_password
user: MyUser
SomeField: foobar
AnotherField: barfoo
url: http://my.url.foo
# self-explanatory - :tab, :space (might be useful for checkboxes), :enter
# :delay / :sleep - sleep before proceeding further
# :otp / otp - generate and type an OTP
# path - fill in path of that password entry (in this case
#   mail/gmail/john@gmail.com)
autotype: SomeField :tab user :tab AnotherField :tab pass :space :enter
```

## OTP

The format for OTPs should be `otpauth://[...]`.

Or it should define a method for generating OTPs:

```
otp_method: /opt/obscure-otp-generator/oog --some-option some args
```

## FAQ

### rofi gopass hangs after selecting password

To access passwords your GPG agent needs to have unlocked your secret key you're
using to encrypt your passwords with. If the secret key hasn't been unlocked yet
it will prompt for the secret key password using `pinentry`. If `pinentry` is
configured to read from a `tty` then `rofi-gopass` will hang indefinitely. To fix this
you need to [configure gpg](https://wiki.archlinux.org/title/GnuPG#pinentry) to use a gui version of `pinentry`. Afterwards you may
have to kill the GPG agent if it's still in a bad state: `killall -9 gpg-agent`.

### rofi menu unexpectedly exits upon performing some action

If you have 'notify' option set to `false` in config (it's a default) you might
sometimes just not see some 'Failure' messages and just see the script silently
exiting after you tried some action.

The script isn't aiming to cover and display all even most obvious *user* errors,
like not initialized password store in given directory or just non-existant dir.
Try running this script form console directly and see displayed errors.

## Disclaimer

This is a fork of rofi-pass-wayland adapted for gopass. It was quickly vibecoded to work with gopass instead of pass. Use at your own risk - it may not follow all gopass best practices or support all gopass-specific features yet.
