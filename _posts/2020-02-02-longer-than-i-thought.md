---
layout: post
title:  "Longer than I thought"
date:   2020-02-02 18:00:00 -0500
categories: rust react redux
---

# Patience is a virtue

Doing all the work on khadga by myself is taking longer than I thought.  That being said, I'm kind
of proud that I'm doing everything here.  This is full stack all the way:

- Front end in react
- Using the bulma css library for UI appearance and functionality
- Front end state managed by redux
- Calls to endpoints in the khadga backend, written in rust using the warp framework
- Writing the noesis webassembly npm library, written in rust
- khadga doing CRUD operations on the mongod database
- All the dockerfile and docker-compose creation, including a rust container for building
- Figuring out how to get the docker image into Openshift Online Container Registry

One piece I still need to work on/learn is travis.

## Why is it taking so long?

I installed the `tokei` program using `cargo install tokei` because I was curious about how many
lines of code I had so far.  It's a program similar in purpose to `cloc` but orders of magnitude
faster.  I was surprised that in about a month's worth of work, I'd only written about 1100 lines of
typescript and about 500 of rust.

| Language            | Files    |   Lines     |   Code   |  Comments    |   Blanks
|---------------------|---------:|------------:|---------:|-------------:|---------:
| CSS                 |    2     |   10847     |    9139  |          3   |      1705
| Dockerfile          |    1     |      19     |      12  |          3   |         4
| HTML                |    2     |      29     |      27  |          0   |         2
| JavaScript          |    4     |      41     |      39  |          0   |         2
| JSON                |    5     |   14838     |   14838  |          0   |         0
| Markdown            |   34     |    4902     |    4902  |          0   |         0
| Rust                |   11     |     738     |     558  |         79   |       101
| Shell               |    2     |      34     |      25  |          4   |         5
| TOML                |    4     |      86     |      63  |         12   |        11
| TypeScript          |   26     |    1468     |    1157  |        108   |       203
| YAML                |    3     |      32     |      32  |          0   |         0
|---------------------|----------|-------------|----------|--------------|-----------
|Total                |   94     |   33034     |   30792  |       209    |      2033

Admittedly, there's more work here than just typescript and rust.  I'm not sure where all that JSON
comes from, because I told tokei to ignore any docs folders.  Also, tokei will read in .gitignore
files it encounters, so it won't read in any extra folders (eg, node_modules, or directories that
you compile your typescript to).

I felt like 1600 lines of code in a month's work seemed pretty slow.  But why?

### React, redux and typescript

Some of the time is taken up because I'm not terribly great at react and redux, though I think I'm
getting better.  A lot of the time is spent hooking up state with the components and making sure
everything is wired up.  This is where unit tests really do help.

Although it honestly only took about a day to learn redux and react-redux, the hard part is creating
all the glue in the react components.  This is achieved through the `connect` and `ConnectedProps`
functions in react-redux.  That part is not hard.  It's more like managing the state store, and the
types in the state store that's a challenge.  The reason I'm using react-redux is because I have
components that are not direct descendants that need to communicate with each other, and this is one
of the use cases that is applicable for redux (otherwise, you could use Context in react).

On one hand, doing this in typescript is a little painful, because I have to think about the types
of the store, the types of the reducers, and what portion of state is being passed along in the
`mapStateToProps` function.  On the other hand, once it passes the tsc compiler, it basically just
works.  And the refactoring has been a breeze when I needed to extend or change the type of the
store or the reducers.  I can imagine how painful this would have been had I just used javascript,
so I definitely think using typescript is worth it, even in this relatively small application.

I've been struggling with a myster react/redux problem.  Currently, the app has a modal that will
appear when the user clicks the `Log In` button.  It actually works great, and if the user types in
the right password for the username, the request reaches out to the backend, which then queries
mongodb to see if the password matches.  All that works fine.

However, if you select `Cancel` on the modal, and then click `Log In` again, whatever the user
previously typed in is still there.  That's obviously a no-no security wise.  At first, I thought
somehow the input fields weren't being re-rendered.  I tried to create my own
`shouldComponentUpdate` method, but because redux's `connect` method returns a new wrapped class,
that didn't work.  However, I eventually figured out that the component is indeed re-rendering when
new state happens, and that new state has a blanked out username and password.  And yet, in the DOM,
the input fields still have whatever the user typed in.

The `onChange` handler is working alright.  So I am wondering if maybe if the state changes, I need
to somehow invoke the handler, because that works for sure to clear or set things.

### Warp framework

Some of the time was spent in figuring out how rust's warp framework works.  I sort of understand
the concept of Filters, they are like middleware in many other web frameworks.  Rust's closures
makes understanding the types quite difficult though.  This is somewhat eased by using impl or dyn
traits though.  But, there's still a bit of time eaten up going through the warp docs and through
the examples directory.

One of the bigger challenges was figuring out how to split out my handlers from the `main` function.
Unfortunately almost all the basic examples showed all the handlers for the routes being defined
inside the `main` function, which seemed odd to me.  As you get more handlers, or your handlers
start getting very large, you really don't want a ginormous `main` function to handle the logic.

Also, warp uses closures a lot, but figuring out the type of a closure can be pretty tricky.
Eventually I figured out how to define my handlers in its own module, and then import them in from
the main.rs file.  The best help here was to look at the [examples][-warp-examples] directory on
github.

### Webassembly for noesis

There's a lot of subtleties involved in using rust and wasm-bindgen to build webassembly.  I have
spent a fair bit of time going over the wasm-bindgen docs trying to get better at allowing rust to
build webassembly that can be consumed by other javascript code.

For example, there is a `JsValue` that represents an opaque javascript value.  This is not to be
confused with `Object` which is an actual javascript Object.  This is important to know, because
exported [rust data in arrays have to be a boxed slice of type JsArray][-boxed].  If you try to do
something like:

```rust
#[wasm_bindgen]
pub struct Foo {
	name: String
}

#[wasm_bindgen]
pub fn export_a_vec() -> Vec<Foo> {
	let arr = vec![Foo { name: "sean".into() }];
	arr
}
```

it won't work.  As the documentation says, to return an array of some values, first, it has to be a
slice of JsValue.

By the way, the Foo struct itself won't even compile.  You'll get a compiler error saying that Foo
doesn't implement Copy.  You can't implement it yourself either, since the name field is a String
type, and it doesn't implement Copy.  What's strange to me though, is that you _can_ have an
exported function in rust that returns just a String.   It seems that fields in a struct that don't
implement the Copy trait are off limits however.  I'm going to take a look at that deeper though,
since that doesn't seem right.

So how _do_ you return an array of something?

Here's an example of how I returned a list of media devices enumerated from window.navigator:

```rust
#[wasm_bindgen]
pub async fn list_media_devices() -> Result<js_sys::Array, JsValue> {
    let navigator = get_navigator();
    let media_devs = navigator.media_devices()?;

    // Create a javascript array
		let devices = js_sys::Array::new();
		
		// enumerate_devices() returns a Result, where Ok is a device, and Err is an error of some sort
    match media_devs.enumerate_devices() {
        Ok(devs) => {
					  // The Ok value is actually a Javascript Promise.  So we convert this to a Future
            let media_device_info_arr = JsFuture::from(devs).await?;

            // Attempt to convert the media devices into an iterator.
            let iterator = js_sys::try_iter(&media_device_info_arr)?
                .ok_or_else(|| {
                    console::log_1(&"Could not convert to iterator".into());
                })
                .expect("Unable to convert to array");

            // Walk through the iterator, and push the result into our devices Array
            for device in iterator {
                let device = device?;
                let device_info = device.dyn_into::<MediaDeviceInfo>()?;

                let stringified =
                    js_sys::JSON::stringify(&device_info.to_json()).unwrap_or("".into());
                console::log_1(&stringified);
                devices.push(&device_info);
            }

            Ok(devices)
        }
        Err(e) => Err(e),
    }
}
```

With all the above going on, I'm very curious if this is any faster than just using raw javascript.
I'll have to test this out sometime.

But, there are a lot of intricacies to writing rust with [wasm-bindgen][-wasm-bindgen].  Just
looking at the book, and you can see all the details.  Even understanding the Cargo.toml file can be
a little tricky.  Then there's also [wasm-pack][-wasm-pack] to get familiar with.  One of the
trickier aspects of wasm-bindgen is knowing which features to add to your Cargo.toml file to make it
work.  For example, if you are getting an HtmlElement from the DOM, you need to add the `Document`
and `HtmlElement` feature flags.  For example:

```toml
[dependencies.web-sys]
version = "0.3.33"
features = [
	"Navigator",
	"Document",
	"MediaDevices",
	"HtmlElement",
	"Window",
	"MediaStream",
	"MediaDeviceInfo",
	"console"
]
```

### Docker 

I'm not a docker expert.  I've built a few dockerfiles before and 2 other docker-compose files
before.  I can mount volumes and bind host:container ports.  But there's still a lot of stuff about
docker that I'm not familiar with.  That being said, nothing's been too hard with docker, but I wish
there was a better way to debug issues.

I spent a fair amount of time creating a Dockerfile.dev file that builds an image that has rustup
and nvm installed on Fedora 31.  I wanted to have an image that can build the khadga project.
Moreover, I wanted to be able to dynamically edit my front end bundle, so that I could more easily
test what I was doing on a "real" system.  It always confuses me to translate host system to
container file system, and I invariably get the wrong paths, even if I use `WORKDIR`.

One thing that I haven't done before that I am figuring out how to do is upload my own image.  I am
planning on using Openshift Online for my application, and one of the ways to run your app is to
load it as a docker image.  But it has to be in some kind of image repository like docker hub or the
Openshift Container Registry.  I'd prefer it to be on OCR, but the documentation is very slim on how
to do this.

### Openshift Online

I've been reading the documentation a lot to try to get a handle on how to do a custom application.
OO really wants you do create a project with one of their standard languages or other templates
(like mongod, jenkins, mysql, etc).  They have an option to build a project around your own docker
image.  But it gets more confusing if you have multiple services.  They use `pods` to handle
multiple services, and then once all your services are in the pods, you can add them to the main
cluster.

I'm also trying to figure out how to do DNS in Openshift Online.  I haven't found any good info on
this so far, although they do have a section about [DNS][-DNS], but it doesn't give much info.
There's also the developer guide section that I need to check out.  I also need to figure out how to
create a LetsEncrypt TLS cert, and automate the renewals.

I'm getting close to a point where I can put up a super-rough (and not yet very secure) alpha
version of the application.  So I need to get on top of this pretty soon.  I'd like to be able to
demo this off, even if it's totally alpha.

### WebRTC 

I've slowly been going over the MDN documentation on how to get and list MediaDevice and MediaStream
objects.  That's on top of figuring out how to create a signaling service.  There's some sample code
on how to do this in MDN, but I need to translate it from javascript to rust on the backend and
typescript for the client.

I'm doing baby-steps now to just get the local webcam.  In fact, I could do a lot just with that.
One of the main goals of the project is to do deep learning.  For facial recognition I just want to
grab data from the MediaStream both for training and as actual data to present to the layers.
Eventually I'd like to do object tracking as well.  For example, have a webcam sitting on top of
some embedded devices that control servos that can point the camera to keep the moving in the field
of view.

[-warp-examples]: https://github.com/seanmonstar/warp/tree/master/examples
[-wasm-bindgen]: https://rustwasm.github.io/wasm-bindgen/introduction.html
[-wasm-pack]: https://rustwasm.github.io/wasm-pack/book/
[-DNS]: https://docs.openshift.com/online/pro/architecture/networking/networking.html#architecture-additional-concepts-openshift-dns