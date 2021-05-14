# Event-Driven Approach

In every GTK application that I write, I start by setting up an event handler for processing all of the events received from widgets in the application, as well as events received from background processes. Using this event-driven approach, you can eliminate the need for reference counters, or the need to share any data across widget signals.

In this model, all application state and widgets can be maintained in one central location of your application, or within compartmentalized regions that make sense for your application. Because each widget signal will only require to contain a `Sender` in order to send an `Event` to your event handler, which can then fetch whatever information it needs from any widget in its possession. Therefore, the event handler contains exclusive ownership over all widgets and application state.

## Setting it up

To achieve this, we need an async channel that we can get from the `async-channel` crate:

```rust
let (tx, rx) = async_channel::unbounded();
```

Now we need some event variants that our channel will emit:

```rust
enum Event {
    Clicked
}
```

Then we will attach the receiver to a future which merely loops on our receiver forever:

```rust
let event_handler = async move {
    while let Ok(event) = rx.recv().await {
        match event {
            Event::Clicked => {

            }
        }
    }
};
```

Your source code should now look like so, and you are now ready to continue to setting up a window with a clickable button.

```rust
#[macro_use]
extern crate cascade;

use std::process;

enum Event {
    Clicked
}

fn main() {
    glib::set_program_name("First GTK App".into());
    glib::set_application_name("First GTK App");

    // Initialize GTK before proceeding.
    if gtk::init().is_err() {
        eprintln!("failed to initialize GTK Application");
        process::exit(1);
    }

    // Attach `tx` to our widgets, and `rx` to our event handler
    let (tx, rx) = async_channel::unbounded();

    // Processes all application events received from signals
    let event_handler = async move {
        while let Ok(event) = rx.recv().await {
            match event {
                Event::Clicked => {

                }
            }
        }
    };

    // GLib has an executor in the background that will
    // asynchronously handle our events on this thread
    glib::MainContext::default().spawn_local(event_handler);

    // Thread will block here until the application is quit
    gtk::main();
}
```