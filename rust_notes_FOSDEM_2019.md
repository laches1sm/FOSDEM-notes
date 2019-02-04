# Beyond the first steps
- Specical feature of Rust docs called headers that errors/panics can be placed in
- How do you go from begining in Rust to writing in Rust?
## Connecting and sharing
- Rust community has 90+ meeetups worldwide
- Try speaking at Rust events
## Evolving with Rust
- Operates on a crate bound
- `cargo fix`
- Crates are backward completly
- Test on nightly
- Tell people to look at the CI scriot
## Desiging your API
- In Rust, much of it is tradeoffs
- Complexity vs convience etc...
- Do not clone without the user knowing
- `as_ref()` you can pass mostly everything
- Iterators implement into Iterator
- `[#must_use = "Context message"]`
- Communicate via warning to users that something must be used
- Noneable arguments - your user doens't have to worry about writing some
- It's ugly, but use tuples for flexible arguments 
- From implementations on things you want to pass in
- Destructure and take from arrays in Rust 2018
## Dealing with Failure
- Move to use the Faliure library 
- You can't downcast or backtrace the cause of an error
- The failure crate allows to do both of those things
- Prototyping - string errors
- Error 
- Error/ErrorKind
- Define the error with a context that displays an erorr message 
- The error is an enum 
- Implement Fail for an error
- Backtrace and a cause 
- Implement Display
- Implement some Froms - important that you implement both for the enums and the context since we need to access the context
- You can access the context
- You don't need context, you just really need the enum
## Fuzzing 
- Fuzzing doesn't replace unit tests but it's complements them
- Ex you could have fogotten unit tests etc
- Don't block PRs on fuzz failures
- Travis lets you determine an event type
- If it's a cron job, you can enable fuzzing on it
- Explores the state space
- Not going to run over the entire space 
- Only going to run a few thousand every time it's run
- proptest crate 
- Quickcheck family of fuzzers - you define some properties that will be fuzzed over 
- Remebers regressions for you and reminds you to fix failed tests
- Use 'Strategies to flexibily define a state space
- Overflow is a nasty problem so keep this in mind
- Key-value - implement a function that defines a stragty 
- prop_map - map to this new structure, that will return an artibutary key-value pair 
- State space on this is huge 
## Debugging beyond println!
- Bugs can be unpredictable 
- Reasioning about bugs needs information and luck
- rr 
- Record the failure once and debug the recoding determinsitically
- Set breakpoints and watchpoints 
- Ececute and reserve execute 
- `rr record` and once a failure is found `rr replay`
- Only emulates a single core machine 
- Only works on Linux
## Injecting Failures 
- Stable test cases can be hard 
- Test:fail - crate implemented in Rust 
- Fail points are code instrumentations that allow errors and other behaviour to be injected dynamically at runtime 
- `fail_point!("fail-point-name");`
- `fail_point!("fail-point-name", |r| r.into());`
- Confiure a fail point through env vars
- Use the interface in tests 
- Actions are defined like so : ` actions := action[->action...]`
- or `action := [p%] [cnt*]task[ (arg) ] `
- Supported tasks include panics...
- Fully optimized out at compile time (pay for what you use) 

# Sans I0: Safe and Testable Network Protocols 
## Why Async networking matters
- We need to think in a blocking way
- This is not good for performance 
- For multiple concurrent users, you need one thread or process per user
- How handle more than 10000 concurrent connections
- Network is not blocking, that's not how it workds 
- Things are always happening concurrencly in the network side
- The OS handles network indepedntly from your application
## IO Events
- The OS generates events for sockets, that we can resgister with
- Events contain "this socket has data to read", "this one has room in the wriote bnuffer", "this one is closed"
- Can be used to wake up a thread
- or passed to the user to handle them maunally
- Epoll call, manual state machines
- Wait (blocking) on new events
- Green threading, code will be interrupted and storeduntil and event is avaliable for the socket we want
- it emulates blocking IO.
- Complete abstraction, you don't have to think what's inside the system
- Callbacks, you receive a callback, move to async-await framework
- Futures - build a tree of structures representing the computation 
- bit more difficult to write 
- Async/await synxtax is coming in rust - just need to agree on the syntax
## Issues with Futures
- They are hard to debug, any steps inside are reported in the stacktrace 
- Unit testing relies heavily on mocking 
- Typing issues - instead of returning the entire type - they can return entirly different types 
- Different written types 
- Have to think about it everytime you branch in a future
- Futures try to emulate blocking IO but push the responsbility onto the user.
- The abstraction is leaking 
## How do I implement a protocol that is:
- Resuable outside Rust
- Not tied to a particular async IO flavour 
- Easy to test 
-
