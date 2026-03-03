---
title: An Agent is More Than Its Brain
author: Dachary Carey
layout: post
description: In which I talk about what's inside a coding agent.
date: 2026-03-02 20:00:00 -0500
url: /2026/03/02/an-agent-is-more-than-its-brain/
image: /images/agent-more-than-brain-hero.jpg
tags: [ai]
draft: false
---

Apparently I live in the "agent" space now, so let's talk about what's inside an agent! I've been seeing conversations, and learning more myself, and have realized that maybe a lot of people haven't stopped to think about what the component parts of an agent actually *are*. For the purposes of this article, I'm talking specifically about coding agents, but a lot of the bits and bobs are generalizable. And here's why this matters: the model ("Opus 4.6," "GPT-4o") is just one piece. The same model can behave very differently depending on everything else around it.

## The Abstraction: Agent

When we talk about an "agent," we're referring to a shared abstraction. The term "agent" is a label that someone applied to a particular bundle of capabilities: an AI system that can take actions, use tools, and work through multi-step tasks with some degree of autonomy. But the word itself is doing a lot of work, and it can obscure the fact that there are many distinct pieces inside the thing we're calling "an agent."

Think of it like the word "car." A car is an abstraction for a thing with an engine, wheels, brakes, a steering system, and a bunch of other components. You don't need to understand all of those components to drive one. But if you want to understand *why* one car handles differently from another, or why your car is making a weird noise, you need to start looking at the parts.

Same deal with agents. If you want to understand why your agent behaves the way it does, why it's good at some tasks and bad at others, or why the same question gets different results in different tools, you need to look at the component parts.

## The Agent "Brain" is the LLM

If you've used an agent, you've probably seen a name somewhere in the edge of a UI, where it says something like "Sonnet 4.5" or "Opus 4.6" or "GPT-4o." This name refers to the LLM powering the agent. Think of it like the agent's brain.

The LLM is the part that "thinks." When you give the agent a task, the LLM is what reads your request, decides what to do, interprets the results it gets back, and generates the text you see in response. Different LLMs have different strengths. Some are faster but less capable. Some are better at reasoning through complex problems. Some are better at generating code in specific languages. The model choice matters, and it's one of the first things people look at when evaluating an agent. But here's the thing: the brain is only *one part* of the agent.

A brilliant brain in a body with no arms can't pick anything up. And an agent powered by the best LLM in the world can only do what its platform gives it the ability to do. The rest of this article is about all those other pieces.

## Agent Interfaces

If an LLM is the brain, the interface is how you give instructions to the brain. Agent platforms vary, but interfaces typically come in a few forms: the chat interface, or behavior triggers. The chat interface is where interpretation happens. Behavior triggers are more deterministic, although they can *invoke* interpretation. But understanding both gives you the ability to fine-tune agent outputs.

### The Primary Interface: Chat

The most visible part of any agent is the chat interface. You type something, the agent responds, you type something else. It feels like a conversation.

Each time you send a message, that's called a "turn." Your messages are "user messages" and the agent's responses are "assistant messages." The agent considers the full history of this back-and-forth when deciding what to do next. Your earlier messages provide context for later ones, which is why an agent can (usually) remember what you asked it to do three messages ago.

But here's where it gets interesting: your message is an *input*, not a *command*. When you type "fix this bug," you're not invoking a function called `fixBug()`. The LLM *interprets* your message, decides what it thinks you mean, and then figures out what to do about it. This is why the same prompt can get you different results on different runs. The LLM isn't executing a deterministic script; it's making a judgment call every time.

This is also why *how* you phrase things matters. A vague prompt gives the agent more room for interpretation (and more room to go sideways). A specific prompt with clear context narrows the possibilities. You're not programming the agent; you're communicating with it. And like any communication, clarity helps.

There's a double-edged sword here, which is related to how LLMs respond to inputs. If your command is very specific (lots of details, long and involved, like a very detailed prompt) the LLM may be less likely to consider anything *outside* of that command. A few overlapping research findings explain why.

First, LLMs are trained through a process called reinforcement learning from human feedback (RLHF) to be helpful and follow user instructions. This training creates a strong bias: when you give detailed, specific instructions, the model shifts into what you might call "execution mode." It prioritizes doing exactly what you asked over reasoning independently about the broader context. Research on [sycophancy in LLMs](https://aclanthology.org/2025.findings-emnlp.121.pdf) has shown that detailed user context actually *amplifies* this compliance tendency, especially in longer conversations.

Second, there's a positional attention problem. A paper called ["Lost in the Middle"](https://arxiv.org/abs/2307.03172) (Liu et al.) found that LLMs pay the most attention to information at the *beginning* and *end* of their context window, and significantly less attention to information in the middle. In an agent, the system prompt is at the beginning and your user messages are at the end. Everything else (project instruction files like CLAUDE.md or AGENTS.md, Skill instructions, earlier conversation history) is in the middle. So the model is already predisposed to weight your messages heavily and give less attention to that middle-context content.

Third, research on prompt specificity itself shows a nuanced tradeoff. A paper called ["DETAIL Matters"](https://arxiv.org/html/2512.02246v1) found that detailed prompts improve accuracy for procedural tasks (math, logic, step-by-step operations) but can *constrain* the model's reasoning on open-ended tasks. The more specific your instructions, the more the model executes rather than deliberates.

Put these together and you get a practical consequence: if your command is *very* specific but you leave something out, the LLM may not fill it in. It sees a detailed series of instructions and executes them. It's less likely to independently consider other things it would normally consider, like project instructions or Skill text that are sitting in the middle of its context. Your user messages get prioritized by the training, they get positional attention by being at the end, and the specificity pushes the model toward execution over deliberation. Other instructions get, let's say, de-prioritized.

Figuring out the balance here can be tricky. I recommend a lot of experimentation. And the good news is, researchers are discovering these dynamics *all the time*, which means the next generation of models can build on those discoveries and improve.

### Slash Commands, Hooks, and Other Shortcuts

Not everything has to go through the LLM's interpretation loop. Most agent platforms offer ways to trigger specific behaviors directly, bypassing the "thinking" step entirely.

**Slash commands** are the most common example. In Claude Code, typing `/init` creates a CLAUDE.md file. Typing `/compact` compresses your conversation context. These aren't prompts the agent interprets; they're direct commands that invoke specific functionality. The agent doesn't decide *whether* to do the thing. You told it to do the thing, and it does the thing.

**Hooks** are another mechanism. These are scripts that run automatically in response to specific events. For example, you could set up a hook that runs a linter every time the agent edits a file. The agent doesn't choose to run the linter; the hook fires automatically based on the event. Hooks let you enforce constraints or trigger workflows without relying on the agent to remember to do them.

**Skills** are a more recent addition. An Agent Skill is a bundle of instructions and reference material that gives the agent just-in-time context for specific tasks. When you install a skill for, say, a specific framework, the agent gets access to detailed guidance about how to work with that framework. Skills sit somewhere between "tool" and "instruction set." They augment what the agent knows and can do, targeted to a specific domain.

Slash commands and hooks matter because they give you ways to interact with the agent that don't depend on the LLM's interpretation. If you need something to happen *reliably and consistently*, they're more predictable than a chat message or a project instruction file.

Skills are a different story. Because they work by adding context that the LLM interprets, they're subject to the same attention and prioritization dynamics we just talked about. A well-written skill helps, but it's not a guarantee the way a hook is.

## Agent Tools

So far we've talked about the brain (the LLM) and how you communicate with it (interfaces). But an agent also needs to be able to *do things* in the world. That's where tools come in.

When an agent reads a file, searches your codebase, runs a shell command, or fetches a web page, it's using a tool. The LLM decides *which* tool to use and *what arguments* to pass, but the tool itself is a separate piece of software that actually executes the action and returns results. The LLM can't read your files directly any more than your brain can directly sense what's inside a closed box. It needs hands (tools) to interact with the environment.

### Harness Tools

Every agent platform comes with a set of built-in tools. These are the tools that the platform developers decided the agent should have access to out of the box. I think of these as "harness tools" because they're built into the harness that wraps around the LLM.

Common harness tools include:

- **File operations**: reading files, writing files, editing files, searching for files by name or content
- **Shell execution**: running commands in a terminal
- **Web access**: fetching content from URLs
- **Code search**: searching across your codebase for patterns or definitions

Different agent platforms provide different sets of harness tools, and the *implementation* of those tools can vary significantly. Two platforms might both offer a "read file" tool, but one might return the entire file while the other truncates it after a certain size. One platform's web fetch tool might follow redirects while another doesn't. These differences in tool implementation directly affect what the agent can accomplish, even when the underlying LLM is identical.

This is one of those things that's easy to overlook. If you switch from one agent platform to another and notice the agent is suddenly worse at certain tasks, the problem might not be the model. It might be that the new platform's tools work differently.

### MCP Servers

Harness tools cover the basics, but what if you need your agent to do something the platform didn't anticipate? That's where MCP (Model Context Protocol) servers come in.

MCP is a standard that lets you give an agent *new tools* by connecting it to external servers. An MCP server exposes a set of capabilities (tools, resources, prompts) that the agent can discover and use, just like it uses its built-in tools. The difference is that *you* choose which MCP servers to connect, and anyone can build one.

For example, you might connect an MCP server that lets your agent interact with a database, manage your cloud infrastructure, or query an internal API that your company built. The agent platform doesn't need to know about your specific database or API ahead of time. The MCP server handles the specifics, and the agent just sees a new set of tools it can use.

This is a powerful extensibility mechanism. It means the set of things an agent can do isn't fixed by the platform developers. You can customize and extend it based on your own needs. But it also means that two people using the "same" agent with different MCP servers connected will have agents with very different capabilities.

The inverse is true, too, though. The nice thing about MCP servers is that they're tools you can take with you, and they behave the same across platforms. Harness tool implementation may vary. But MCP server implementation is baked into the server, not the harness. So if you use an MCP server with Claude Code, and use the same MCP server with GitHub Copilot, the tool implementation is *exactly the same* in either platform. The agent might still *use* the tool differently (passing different arguments, calling it at different times, interpreting the results its own way), but the tool itself is consistent. That's one less variable to worry about when you're trying to understand why your agent behaves the way it does.

## Brain != Behavior

Here's where it all comes together, and where I think a lot of the confusion about agents lives.

People tend to equate the agent with the model. "I'm using Claude Opus 4.6" or "I'm using GPT-4o" becomes shorthand for the entire agent experience. But as we've seen, the model is just one component. The agent's *behavior* (what it actually does, how well it does it, and how it responds to you) is determined by the combination of *all* these pieces:

- **The model** determines the quality of reasoning, code generation, and language understanding.
- **The system prompt** shapes how the model behaves. This is a set of instructions that the platform provides to the model before you ever send your first message. It tells the model things like "you are a coding assistant," "prefer editing existing files over creating new ones," or "ask before running destructive commands." You usually don't see the system prompt, but it profoundly influences the agent's personality and default behaviors.
- **The tools** determine what the agent *can do*. An agent with no file-editing tool can't edit files, no matter how smart the model is.
- **The temperature** controls how much randomness is in the model's outputs. A low temperature makes the model more predictable and focused; a high temperature makes it more creative and varied. Different platforms set this differently, and some let you adjust it.
- **Context window management** determines how much of your conversation the agent can "remember" at once. When the conversation gets long, platforms use different strategies to compress or summarize earlier messages. How well this works affects whether the agent loses track of things you told it earlier. If you've ever been in the middle of a long session, and your context got compacted, you know that what went in is not the same as what came out.
- **Permission and safety systems** define what the agent is *allowed* to do. Some platforms ask before running shell commands. Others auto-approve certain actions. Some restrict file access to specific directories. These guardrails shape the agent's behavior just as much as the model does.

So when someone says "Claude Opus 4.6 is great at coding," what they probably mean is "the agent I'm using, which happens to be powered by Claude Opus 4.6, with this specific set of tools and this system prompt and these permission settings, works well for my coding tasks." Two different agent platforms can both use Claude Opus 4.6 and give you noticeably different experiences, because everything *around* the model is different.

This is why I prefer Claude Opus 4.6 in Claude Code to Claude Opus 4.6 in GitHub Copilot; the Claude Code harness and system prompt produce outputs that are more to my liking for the types of tasks I often perform.

This is also why it's worth paying attention to which platform you're using, not just which model. The model matters, absolutely. But so does the harness. An upgrade to a better model might make less of a difference than you'd expect if the tools and system prompt are poorly designed. And a less powerful model in a well-designed harness can sometimes outperform a more powerful model in a weaker one.

The inverse can be true, too. I've been using Claude Code a lot in my personal projects lately. I mostly work with Opus 4.6 these days while occasionally falling back to Sonnet 4.5. I just got access to Claude Code at work, but *that* instance uses models available through AWS Bedrock. The default model is an older one, and I noticed it was significantly less capable of the architecture and system brainstorming I've really enjoyed with Opus 4.6. Both are using Claude Code, so the harness, system prompt, and tools are the same - but the underlying model *isn't*, and that made a much bigger difference than I realized. Models have come a long way in a year.

Understanding all these components won't make you an AI expert overnight. But it gives you a framework for thinking about *why* your agent behaves the way it does, and where to look when something isn't working the way you expect. Instead of a black box that magically produces code, you can start to see the levers and dials that shape the output. And once you can see the levers, you can start to pull them.
