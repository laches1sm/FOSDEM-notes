## FOSDEM 2019 Go devroom


#### Librehost

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

#### State of Delve, a debugger for the Go programming language

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


#### Designing Command-Line tools People Love

- How to _design_ a CLI tool that is easy for people to use and people can contribute to 
##### Design Goals
- CLI that is predicable - if it's in a ecosystem you can guess what commands to use without the help text
- Task oriented commands - make the task at hand easier for example making keypairs, turning it into one simple command
- Friendly to both humans and scripts 
- New contributor launchpad - always onboarding new contriubtors, since CLI tools are very popular, easy for people to get into 
- It's a great place to get new contributors into your porject
- Docker Version Manager, K8 Service Catalog, Porter (related to K8)
##### Recommendations
###### Command Design
- If you commit code and someone starts learning and they're going to try and chang it
- Pick a grammar, helm list etc
- What's nice when you pick a grammar, it sounds like a sentence 
- Anything that you can do to help people to learn these commands, will help get people to use your tool more
- When you're selecting these nouns and verbs, you want to be consistent.
- I.e. helm is not consistent, helm install vs helm remove - this will cause people trouble every single time they use the CLI tool 
- Not figuring out the commands you have in the future
- Figure out what you'll do next 
###### Understand precedent in your ecosystem
- svcat follows kubectl 
- dvm followed nvm
- dep doesn't follows glide, npm, etc.
- You can't make changes because it will make people's lives harder if you're suggesting changes in an already established ecosystem
- however if there's something spefic in the domain, you can add it, but don't change established commands
- If it does the same things, use the same commands.
- If it doesn't work the same way under the hood, don't use the same syntax!
###### Let's design a CLI!
- Reusing verbs that you already have
- Being nice to scripts - parsing this in bash will suck and let people choose the output of your CLI
###### Domain vs. Grammer
- Use your judgement about the domain when breaking with the grammar
- The command should tell you what the domain is 
- Make tasks eaiser, don't simiply wrap an API!
- Looks like a code gen client - it isn't enough!
- Add that human layer to it and help people use it
###### Frameworks
- Cobra - Commands and Flags
- Viper
- Afero
###### Cobra 
- The code that matters is that we're instalizing a new Cobra command
- Built on a bunch of different things 
- Add in subcommands
- Cobra takes over the main func 
- If there's an error, it will handle it, printing out output
###### Viper
- useful for output format arugments
- Viper can be given the first part of the file name and look for it in all the file formats
- Loads it into memory and unmarshalls it into a stucture
- Some people don't like the fact you can read in your config env from anywhere 
- It means that anyone who uses your code needs Viper - weird dependices
###### Afero
- Replaces ioutil 
- Super sutble 
- Stops using hardcoded ioutil, you can mock it out and hit something else entirly. 
- Can hit HTTP as well
- Easy to swap out, useful for tests
- How do we make this testable?
###### Package structure
- cmd/* is the wiring
- This is where the main is, keep this tiny as possbile, can't be reused 
- Anything in here can't be exported
- pkg/* put packages here
- Make functions that are 1:1 mapping the commands in your CLI
- Create happy little packages for everything
- Hide your band-adies and API wrappers in here
- Can take one of your functions and reuse it
- Makes testing a lot easier - one function with corrospending tests
- Very few tests for the main packages, all the tests are in the other packages
- One of the things that is very important is that the second packages, inject things like the console or filesystem etc.
- Context is useful for mocking things out 
- Use it like a CLI but through code

### Go containers? Go serverless? A cloud native jounery
#### The Monolith
- How to modernise a monolith? Or how to make it cloud native
##### Containerized microservices
- First we step back a bit, k8 is the main backend of the system
- Container lifecycle management
- Focus on developing application 
- Spilting up the monolith into several microservices - one for uploading the image and another to extract the metadata
- Everything is stateless 
- How do you break down the monolith
- life and shift - put the monolith into a pod
- both microservices as containers in one pod share data via local vloume
- app containers in different pods, share data via persistent volumes
##### Serverless
- Really just an umberlla term for function as a service, databases and datasotries, object storeage, message queues, and query 
- leave the whole operation part to something else 
- event-driven (needs a trigger i.e. HTTP call)
- short running 
- stateless (externalize the state/intergrations)
- Three lamnbda functions:
- upload image, list images and metadata
- metadata extraction
- Using S3 as shared storage for images and metadata and static assests for UI
- Leaverages cloud function
##### Alternative serverless archittures 
- trigger metadata extraction on s3:ObjectCreated etc
- Containers and serverless: similar yet diffetent 
- lift and shift isn't possible with serverless, need to re-artecht
- local dev is limited with lambda
##### The good
- Containerzied microservices: K8 provices portablity
- Servless: devs can focus on business logic
- Both containers and serverless:
- ratio of "code to config"
##### The bad
- containerized microservices:
-  handling container images
-  DX is poor 
- serverless:
-   lanaguage depended latencies
-   state hydration 
- how straightforward is auth? not a big problem, big problem was parsing the payload though the lamdba function
- How do you ensure that everything runs? either you have a handful of functions or you're in a bad place
- if you have a lot of functions, you need some kinds of orstraction, you need to write that yourself 
- If you have a k8 cluster, it is perfectly possible to run SaaS frameworks on top of K8. 


### gRPC, Protobufs and Go
#### Machines talking to Machines
- New implementations of old ideas, i.e. JSON-RPC, REST...
- Simple idea I want to invoke and action on a remote machine
- gRPC usues HTTP/2, much better perfomance
- gRPC should be considered
#### Nice things about gPRC
- Complete solution out of the box
- Protobufs are hidden with gPRC
- Advanced features - bidirectional streaming, flow control, blocking and unblocking
- Build in a reverse proxy for REST 
- Protocol biuffers - lightweight and fast
#### Distills down to 3 steps
- Create your Interface Definition (proto file)
- Implement the Server
- Implement the Client
- And implement the API....
- It's easy to build a gPRC service
- Build our own SAN Storage Device 
- LVM and loopback devices and put it on a server
- iSCSI target and initiator 
#### Step 1 - define your interface
#### Step 2 - define your client
#### Step 3 - implement a client
- Make sure you're in your GOPATH
- proto file is like the nuts and bolts 
- The first thing we want to define is the message 
```
syntax = "proto3"
package lvm

message Volume {
   string name = 1;
   }

message CreateVolumeRequest{
      string name = 1;
      string volumeGroup = 2;
      string pool = 3;
      uint64 size = 4;
      }
      

 message CreateVolumeResponse{
     string message = 1;
     }

     service `lvm`[
     rpc Create Volume`8CreateVolumeRequest`0 returns (CreateVolumeResponse){}]
     
     ```
- Server is just standard Go, we take the Context which the gPRC stuff will take back and forth
- The server is where LVM is installed so just executing it 
- Setting up the server with gPRC is simple 
- We need a client, just put the calls to our client, and wire in the IP address of the server


