# Agents Can Code. They Can't Decide.

There's a pattern I keep seeing. Someone hands a coding agent a project. The agent executes. The code works. Tests pass. And then, a few weeks later, things start quietly falling apart — not because of a bug, but because of a choice made early on. The wrong database for the access pattern. A queue where a simple job runner would've been fine. A microservice boundary that made sense in theory but turned the whole thing into a distributed monolith.

The code was fine. The architecture wasn't.

---

What a senior developer actually brings to a project isn't just coding ability. It's a kind of pre-loaded decision map. Before writing a single line, they already know which database fits this use case, which queue strategy makes sense at what scale, where the invisible ceilings are. Not because they looked it up — because they've already been through the moment where the thing broke and had to move off it.

That's the gap. The agent is an excellent executor. The system design judgment is in the person directing it.

When the person directing it *is* a senior developer, things go well. They think it through, break it into clear tasks, hand it to the agent, and the agent fires through it. The hard work — what to build and with what — happened before the agent touched anything.

But most people using agents right now aren't senior developers. And even developers who are solid coders sometimes haven't had enough breadth of production experience to know, say, when Cassandra becomes the wrong call, or why your MongoDB setup is going to start hurting at a specific scale.

---

There's a distinction worth making here, though. If you're building something for yourself — no other users, no scaling concerns, just automating something from your own workflow — this gap barely matters. The reason is that what you're bringing to the table is something the agent genuinely can't replicate: deep domain knowledge of your own process. You know every step, every edge case, every place it breaks. That clarity is actually what makes the agent effective. You're the expert on the *what*, they handle the *how*. Agents are surprisingly capable in this mode.

The problem starts the moment you need the thing to grow, or serve other people, or hold up under real load. That's system design territory. And that's where both the agent and the person hit the same wall.

---

The obvious response is: just look it up. Read the docs. Search the trade-offs.

The problem is that documentation is a reference, not a judgment. It tells you what something does. What it doesn't tell you is when not to use it, what it quietly fails at in production, what decision Discord made at 10M users that you'd be wise to borrow. That kind of knowledge lives in engineering post-mortems, conference talks, internal retrospectives, the kind of thing someone shares after getting burned.

There are tools trying to address part of this — Context7 and similar MCP doc-fetchers are genuinely useful for one thing: you've already picked a tool, and you need the API right now. "How do I configure a Kafka consumer?" — they go get the docs. That's good. But it's solving a micro-level problem. The question it can't answer is: should you be using Kafka at all? That question doesn't live in any documentation page.

---

Take Discord's message storage history as an example of the kind of judgment that matters.

They started with MongoDB — flexible schema, fast to build on, fine at the beginning. Then message volume grew and the read/write patterns stopped fitting a document store. They moved to Cassandra — better suited for write-heavy workloads, scales linearly. But Cassandra brought its own problems: compaction-related latency spikes, and JVM garbage collection turning into an operational headache. They eventually moved to ScyllaDB — same API as Cassandra, rewritten in C++, GC issues gone, costs down.

Three moves. Each one contains a real decision signal: when does MongoDB stop being the right call? What does Cassandra actually cost you, not in dollars but in operational complexity? When is the runtime itself the bottleneck?

A senior developer carries all of that — not as a memorized list, but as pattern recognition. That's what makes them a good system designer. It's a macro skill, not a micro one. It's not "how to use Kafka." It's "when Kafka is wrong for this, and what it'll cost you to find out the hard way."

---

This knowledge already exists. Most of it is scattered across engineering blogs, case studies, post-mortems, talks. The issue is that it's formatted for humans reading linearly — narratives, context, long explanations. That format makes sense for learning. It's terrible for retrieval at planning time.

An agent in planning mode doesn't need to *learn* system design. It just needs to *know the trade-off at the moment of decision*. Those are two completely different information needs, and almost all the existing material is optimized for the first one.

What the agent actually needs is something like: "Use Cassandra when you have write-heavy workloads that need horizontal scale. Don't use it when your team doesn't want to manage JVM tuning and compaction. If you're on this path, look at ScyllaDB." Terse. Judgment-first. Retrievable in two seconds.

---

The structure that fits this best is something like an Obsidian vault. Not a flat list of tools — a graph. Notes organized by domain, connected through backlinks, so the agent can start with a vague requirement, follow the links, and arrive at a set of relevant trade-offs without having to read forty pages to get there. Each entry covers: what it is, when to use it, when not to, what it pairs with, what it silently costs you, and real examples. Short. Dense. Token-efficient.

Some domains are self-contained — caching is mostly its own thing. Others bleed into each other — your database choice affects your queue strategy, which affects your deployment model. The backlinks capture that structure. The agent can navigate it the way a senior developer navigates their own mental model: start somewhere, follow the connections, arrive at the right place.

I put together a starting scaffold for exactly this — the folder taxonomy, the entry template, a few seeded entries to show the format. It's not complete; it's nowhere near complete. But the shape is there. [github.com/saitrogen/system-design-skill-wiki](https://github.com/saitrogen/system-design-skill-wiki)

---

If you've moved off a tool in production because it hit a wall — that decision is exactly what should be in here. Not the full story, just the signal: what you were building, what broke, what you moved to, and what you wish you'd known earlier.

The goal isn't a new platform or a product. It's a structure that agents can actually use when they're planning, so the next person who hands a task to an agent doesn't end up with an architecture they'll regret in six months.