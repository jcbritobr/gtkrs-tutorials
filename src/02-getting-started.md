# Getting Started

## Dependencies

Before we begin, ensure that you have the necessary development files installed for GTK. On Debian platforms, you will need:

- `libgtk-3-dev` for GTK3
- `libgtk-4-dev` for GTK4
- `libwebkit2gtk-4.0-dev` if embedding a GTK WebKit View
- `libgtksourceview-4-dev` if embedding a GTK Source View

On the Rust side of things, you should install:

-  `cargo-edit` with `cargo install cargo-edit`, because that'll make adding dependencies to your project easier.
-  `rust-analyzer` in your IDE so that you'll have instant feedback about warnings and errors as you type

## Creating Your Project

Now we're going to start the process of actually building our first GTK application. Create your project, and add the following dependencies that we need to get started:

```
cargo new first-gtk-app
cd first-gtk-app
cargo add gtk glib async-channel cascade
```

### Initializing GTK

Now we're ready to start with code. Lets start by setting your application's name, and initializing GTK.

```rust
#[macro_use]
extern crate cascade;

use std::process;

fn main() {
    glib::set_program_name("First GTK App".into());
    glib::set_application_name("First GTK App");

    if gtk::init().is_err() {
        eprintln!("failed to initialize GTK Application");
        process::exit(1);
    }

    // Thread will block here until the application is quit
    gtk::main();
}
```