---
layout: post
title:  "The reasoning behind the ideas and approaches"
categories: general
---
# The reasoning behind the Unity related solutions
The solutions and ideas here are usually aiming towards a certain goal.

- Prototype fast
- Release fast
- Iterate fast

To achieve these, I'm trying to follow the approaches below:

- *Reduce unity dependency as much as possible:* Besides all that Unity is good or bad discussion, there are disadvantages to depending on Unity heavily.
  - Multiple programmers working simultaneously may result in merge conflicts more often for unity scenes & prefabs. So for example keeping the data in JSON files rather than on components via Editor allows me to leave the Unity scene untouched most of the time for changes other than UI or view related stuff.
  - Keeping data in JSON files also allows designers to work on Excel sheets, or anything, for game configurations and content rather than going through a Unity scene to change variables. You or even they can easily export the data to a JSON file and put it in the project. It also allows the data to be easily overwritten, by a remote server for example.
  - Deterministic execution order. Rather than depending on unity's lifecycle and implementation details, there is a clear, easily followable execution order for your game logic.
  - Single point of entry. By not depending on Awake, Start in everything but having a single entry point and than propagating everything from there, you can easily insert/remove new functionality in between steps. This also feeds back into deterministic execution order.
  - Being able to pause/resume anything easily. I usually end up wanting a way to pause game, but not pause the entire Unity engine by setting the time scale. I usually want the UI transitions and/or animations to continue while just pausing the game simulation when it's in background. Or vice versa.
  - You can already do some of these without the solutions I provided above of course, but I like having more control over my code, keeping everything visible and within easy reach to the developer rather than a lot of indirection by scattering them all over the Editor. I feel it's easier to build, iterate and debug.

- *Expose configurations to designers easily:* This goes into prototyping and iterating fast. There are a lot of changes and work happening during development and having an easily configurable game is key to iterating fast.

- *Flexible core:* This is probably what everyone wants I guess, but it's usually hard to keep the project intact as it grows. Adding a lot complexity grows the project out of hand. It's important to keep things as simple as possible.
  - Entity component systems. They allow for easy and solid feature implementations, especially as the game gets more complex. For now, I'm using Entitas. Although I believe it's a bit over-engineered and it adds some cumbersome work at times, it's the closest I got for an ECS. ECS is used for the core game simulation only. I make mobile games and they involve a lot of additional content for the meta game. They are not implemented with an ECS.
  - Data driven design.
