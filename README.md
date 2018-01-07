# Exercise 4: From Prototype to Production

1. Don't overengineer.
2. Always design properly.
3. Minor detail change will ruin your perfect model.
4. Always prototype first.
5. You will only know what you didn't know on the second iteration.
6. There is only budget/time for the first version.
7. The prototype will become production.


## Network Module

This exercise aims to produce the "v0.1" of the network module used in your [project](https://github.com/TTK4145/Project) and at the same time prepare you for the network part on the [design review](https://github.com/TTK4145/Project/blob/master/EVALUATION.md#design-review).


You should start by taking a look back at [the beginning of Exercise 1](/Ex02-Intro#1-thinking-about-elevators), and reevaluate them in the light of what you have learned about network programming (and - if applicable - concurrency control). At the same time you might want to look into what kind of [libraries that already exists](https://github.com/TTK4145/Project#state-of-support-for-some-popular-languages) for your chosen language.


### Requirements for the network module
Before starting to implement you should identify requirements for the network module. For the very least, you must define a protocol that allows you to send the information you intend to share. You might need to modify the content you share so this protocol should probably allow for extension/modification of content. [JSON](https://www.json.org/) is a popular choice.


The next thing you should think about is wether you want to exchange "changes" or "worldviews". If you decide to exchange changes, what will happen if a node joins late or rejoins after beeing offline?. If you decide to exchange worldviews, how will you differentiate between a retaken order and an order that never was executed? Remember that picking the "simplest" way will generally give you an advatage (Former students have proven that writing a fault tolerant database in a new language takes a lot of time).


Something closely related to the former point is what network topology you want. Should packets be looped through a ring, broadcasted to everyone or should all communication go through a server? Some things you should think about when choosing a network topology is:
 - Do all your nodes need to "agree" on an order for it to be accepted? In that case, how is a faulty node handled? 
 - How can you be sure that at least as many nodes as needs to agree on the order in fact agrees on the order?
 - What is the simplest network topology to implement?
 - What is the simplest to implement the needed logic on top off?
 - Remember that if you choose a server/client approach
     - The server can die.
     - [Elevators have to be initialized the same way](https://github.com/TTK4145/Project/blob/master/SPECIFICATION.md#51-your-program-should-be-started-with-a-simple-one-liner-it-should-be-started-the-exact-same-way-for-all-participating-elevators-if-some-configuration-is-required-either-as-input-args-or-through-a-config-file-this-should-be-the-exact-same-for-all-participating-elevators-you-might-be-asked-to-hand-in-executables-for-the-completion-test-if-youre-writing-in-a-language-that-require-an-interpreter-andor-have-problems-generating-executables-talk-to-the-student-assistants-or-the-person-administrating-this-project-to-get-help)


Next you will need to find out is what kind of networking protocol you wish to build your module on top of. Will it be TCP, UDP, both, or something completely different?
 - TCP gives you a data stream that is guaranteed received in the same order as it is sent in.
 - UDP might reorder the packets you send into the network.
 - TCP will resend packets if they're dropped (at least untill the socket times out).
 - UDP may drop packets as it pleases.
 - TCP require you to set up a connection.
 - UDP doesn't need a connection and even allows broadcasting.
 - If you are using TCP: How do you know who connects to who?
 - You're also allowed to use any net lib or language feature you may desire.
      - If you're using Erlang you should look into [distributed erlang](http://erlang.org/doc/reference_manual/distributed.html)


You will need to figure out the responsibility for the network module. Should the network module or the "application" do the following things:
 - Serialization/Deserialization of data structures?
 - Detection of non-responsive nodes?
 - Handling of lost messages?
 
 
 At last you need to think about fault tolerance and everything that can go wrong. Imagine different fault scenarios and reiterate over you design to improve it. You might have to fully scrap the network module several times before reaching something useable. Some examples scenarios you should imagine.
 - What happens if one of the nodes loses network?
 - What happens if one of the computers loses power for a brief moment?
 - What happens if some unforseen event causes the elevator to never reach its destination but communication remains intact?

Pencil and paper is encouraged! Drawing a diagram/graph of the message pathways between nodes (elevators) will aid in visualizing complexity. Drawing the order of messages through time will let you more easily see what happens when communication fails.

### Implementation
Now to the "easy" part. Implement your network module in your language of choice. This is probably the right time to create your project github repo. Follow the link on blackboard (if you haven't already) and the repo will be created for you. Use your aquired knowledge about git to check in changes as you implement features.

Don't forget that this module should *simplify* the interface between machines: Creating and handling sockets in all routines that need to communicate with the outside world is possible, but unwieldy and unmaintainable. We want to encapsulate all the necessary functionality in a single module, so we have a single decoupled component where we can say "This module sends our data over the network". This will almost always be preferable, but above all else: *Think about what best suits your particular design*.


By the end of this exercise, you should be able to send some data structure (struct, record, etc) from one machine to another. How you acheive this (in terms of network topology, protocol, serialization) does not matter. The key here is *abstraction*.  

 
### Running from another computer


You can log in to another computer and run your program remotely. Be nice the people sitting at that computer, and try to avoid using the same ports as them.

 - Logging in:
   - `ssh username@#.#.#.#` where #.#.#.# is the remote IP
 - Copying files between machines:
   - `scp source destination`, with optional flag `-r` for recursive copy (folders)
   - Examples:
     - Copying files *to* remote: `scp -r fileOrFolderAtThisMachine username@#.#.#.#:fileOrFolderAtOtherMachine`
     - Copying files *from* remote: `scp -r username@#.#.#.#:fileOrFolderAtOtherMachine fileOrFolderAtThisMachine`
    

## Approval
There is no explicit approval for this exercise as a network module is required to complete the project.

## Extracurricular

[The Night Watch](https://web.archive.org/web/20140214100538/http://research.microsoft.com/en-us/people/mickens/thenightwatch.pdf)
*"Systems people discover bugs by waking up and discovering that their first-born children are missing and "ETIMEDOUT" has been written in blood on the wall."*

[The case of the 500-mile email](http://www.ibiblio.org/harris/500milemail.html)
*"We can't send mail farther than 500 miles from here," he repeated. "A little bit more, actually. Call it 520 miles. But no farther."*

[21 Nested Callbacks](http://blog.michellebu.com/2013/03/21-nested-callbacks/)
*"I gathered from these exchanges that programmers have a perpetual competition to see who can claim the most things as 'simple.'"*
