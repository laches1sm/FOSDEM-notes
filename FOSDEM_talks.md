##FOSDEM 2019 Go devroom


####Librehost

- network of Libre hosting providers
- various models and implications - in an ethical way
- all about free software - 100% free software
- the software that you host needs to be free software
- Barriers for users - most users don't have the technical skills for using free software, librehosts help people with the technical skills to learn it
- People don't know of the FLOSS alternatives or aren't sure where to find them
- a network of shared values 
- values not rules, not an organization, just people who are creating this network together
- cooperation - first value, first thing they want to do to cooperate and work well together
- solidatiry - to help when everything's broken, and solidarity with the average user trying to use FLOSS and linked with activist groups
- love FLOSS, want to use and promote free software
- decentralisation - don't want one big host with a certain set of free software, peer to peer is welcome
- federation - how to work together over loosely connected servers 
- Give people the tools they need to free themselves
- Distrubted platforms - the platform isn't centralized as such 
- Transparency - transparent with the fellow networks and users 
- Fairness - means all sorts of things, but treat the users fairly even if there's no technical knowledge
- Privacy - important, the idea is that libre hosters will not share your data or mine it or not make any money from your data - your data is safe with them
- Data portability, users are not prisioners, their data is theirs, no data locking, help to move or leave network 
- what happens when one actor in the network dissapears, taking care of the users
- bus factor is very high with only one admin, and ask for help to get more admins to help lift the stress
- Public contributions to the commons - stuff should be shared with everyone
- They have a directory of hosters, add by adding a JSON file with your details like the Hackerspace API
- Very much a WIP - only reaching out to libre hosters merging in libre hosters as we speak 
- ongoing discussuin - similar to a French network
- How to do goverenace? Self-gonverence? How to get people to leave if it's not working out? How to tell people they _can't_ join?
- What do you mean by hosting - what layer? Hosting machines or services? Every group does different things, they can host services or servers 
- if you think it fits, it can be adapted
- Which services are provided by different hosters? The first version was planned to have the services and the values of the services they have.
- A problem they have of libre hosters - data migration from one hoster to another is a wishful idea most of the time, it's very difficult.
- Some software supports this - migrating VMs and VPNs is easy, depends on the services that they offer
- Find working groups and peers who want to work on the same issue and want to raise a working group and make things better
- If someone who wants to support a hoster, does the librehoster support that? It doesn't right now, tell them why and support the case
- To have a local hoster that is close by, every city should have at least one librehoster 
- There is nothing done towards fundraising right now but considering it
- No physical servers yet, the network is where you should discus

####State of Delve, a debugger for the Go programming language

- State of Delve - which changed in the last year, what changes have been added and what can you expect from the new features?
- Highest impact areas where people can contribute in the future
- The project is about 4.5 years old now, it's grown a lot and people are using it 
- Building these realtionships with the community and the core team is benefitcal for communitcation for changes that may affect the runtime
- 88 contributors, always looking for more people
##### Changes since last year

- one of the biggest and most exiciting things was the relase of v1.0 and v.1.1.0, after FOSDEM 2018
- considered it stable and ready for use even through it was pre-1.0 
- The version naming follows the upstream naming, they map to Go versions 
- This relase marked for new features, esp of interest to those who want to ingreate it with an IDE
- Adding new feautes - support for 1.10, 1.11, and 1.12, with every new Go release the aim is to improve the debugging information in the Go binaries
- Suggest fixes and contributes code, so that they can provide the best debugging experience
- Make sure that you have the best chance to debug your binaries, new or missing features that are present in the binary
- When you're building an optimised binary, Delve usually strips that away
- Ensure that you can debug any kind of Go binary without any problems
- Support debugging inline functions
- Support for dwz compressed DWARF info - info that is stored in the binary and has information about the programming you're debugging, i.e. what the stack looks like etc 
- Allows for folks to still build binarys with the DWARF information in them, and also build smaller binaries without the DWARF infromation as it can take up a lot of space
- Support for seperate debug infomation - often there's a seperate debugging package in binaries 
- Improvements to the debugging server (running headless without a connection), when you want to run within a container 
- The ability to call functions in the target program, complicated feature, coordination with the Go runtime i.e. garbage collector etc
- Add an 'edit' command - similar to other debuggers, when you're debugging you can stop it and edit the code
- Support Position Independent Executables 
- Show return values on CLI tracing - similar to doing strace, you can trace your Go program with Delve, no need for Println debugging now
- Perfomance improvements, memory caching, list go routines
- The repo has moved to the `go-delve` org 

##### Upcoming features

- Support for 1.13, coming out next year
- Improving the debugging information upsteam and how we parse it 
- Look at the Go issue tracker and there is a debugging label
- More support debugging optimized binaries
- Support for more artchitures 
- Supporting a scripting language - instead of intracting with Delve through the API, looking at Skylark which is used for Bazel, define your own functions etc
- Improve go routine inspection, i.e. where it's located etc
- Improve process inscpection
- Improve stack frame inspection

##### A quick deep dive into function calling from Delve

- The syntax for calling a function is (dlv) call myFunc(arg1, argN...) ... <response>
- Same argument limitations apply 
- In order for Delve to call a function in the target program there is a process to be followed
- Checks if the go routine is actually running
- Save machine registers
- Pushes the current PC on the stack and writes the current argument size, to communicate with the go runtime 
- Set PC (program counter register) to runtime.debugCallV1
- Continue exection 
- Switch back to the runtime
- Runtime copies register contents to stack for the garabge collector to still see those
- Perfoms safe point check
- Allocated stack frame for function call
- Sets AX register to 0 
- Invoke INT3 to switch context to Delve - acts as a breakpoint 
- Back to Delve...
- Write arugment frame
- Set trapped PC as return 
- Set PC to called function and resume 
- The function executes and since we set the return value
- The runtime wraps the panic and sets AX to 2, if successful to 1
- Invoke INT3 to switch back to Delve
- Only pointers to stack allocated objects can be passed as argument - no escape infomation in the debugger right now, can cause memeory corruption issues
- Only some automatic type conversions are supported
- Functions can only be called on running goroutines that are not executing the runtime
- The current goroutine needs to have at least 256 bytes of free space on the stack

