# servo-css-parser
Servo's CSS parser as a usable standalone crate

## Purpose
[Servo](https://github.com/servo/servo)'s [style](https://github.com/servo/servo/tree/master/components/style) component calculates [specified](https://drafts.csswg.org/css-cascade/#specified) and [computed values](https://drafts.csswg.org/css-cascade/#computed) from a tree of DOM nodes and a set of stylesheets.

The main reusable libraries from Servo (which the `style` component depends on) concerned with CSS syntax are the [cssparser](https://crates.io/crates/cssparser) and [selectors](https://crates.io/crates/selectors) crates, providing mechanisms for tokenization, transforming into streams of token values, which translate into generic rules or declarations as a CSS abstract syntax tree. However, these mechanisms have no implementation in particular, which can be tedious to rewrite.

This crate exposes Servo's `style` component as a usable standalone crate, surfacing the CSS parsers and stylesheet-related types directly.

## How to use
This crate concerns itself strictly with parsing the CSS syntax. If you'd like to use some helper macros, or you're just looking to write RSX in your project, take a look at the [RSX compiler plugin](https://github.com/victorporof/rsx_compiler_plugin) instead.

Otherwise, this to your `Cargo.toml` file:

```toml
[dependencies]
servo-css-parser = { git = "https://github.com/victorporof/servo-css-parser.git" }
```

Keep in mind that because this depends on Servo, you'll have to follow the build prerequisites installation instructions [documented here](https://github.com/victorporof/servo/blob/master/README.md#setting-up-your-environment). For example, on OSX:

```sh
brew install automake pkg-config python cmake yasm
pip install virtualenv
```

Then, simply import the library into your code to parse CSS. The parser generates a Servo-powered `style::stylesheets::Stylesheet` (re-exported as `servo_css_parser::ServoStylesheet`) representing the CSS code. See the [documentation](http://doc.servo.org/style/stylesheets/struct.Stylesheet.html) for a complete list of all the data structures.

```rust
extern crate servo_css_parser;
use servo_css_parser::parse;
use servo_css_parser::types::{Url, QuirksMode, MediaList, Origin, ServoStylesheet};

let url = Url::parse("about::test").unwrap();
let origin = Origin::UserAgent;
let quirks_mode = QuirksMode::NoQuirks;
let media = MediaList::empty();

let css = ".foo { background: blue; }";
let stylesheet: ServoStylesheet = parse(css, url, origin, quirks_mode, media);
```