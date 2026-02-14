---
topic: managing-claude-skills
category: skills
date: 2026-02-14
participants:
  - user
  - claude
source: claude.ai
summary: "When to create vs. reuse skills, how Claude triggers them, pitfalls of large skill libraries, and scoping skills per-project in Chat vs. Code."
related_topics: []
---

# Transcript: Managing Claude Skills

## Create vs. Reuse — When to Build Your Own Skill

**User:** When do I create a skill vs get one off the internet?

**Claude:** The short answer: create a skill when your workflow is specific enough that no generic solution will fit. Use an existing one when the problem is well-understood and someone has already iterated on the edge cases.

**Lean toward using an existing skill when:**

- The task is common and well-defined (e.g., generating a `.docx`, creating a slide deck, parsing CSVs). Someone has already hit the weird edge cases and built workarounds.
- There's an actively maintained source — a popular GitHub repo, an official integration, or a curated skill library.
- You need it to work reliably now rather than after a tuning cycle.
- The skill involves format-specific knowledge (like OOXML quirks in PowerPoint, or PDF rendering gotchas) where tribal knowledge matters more than custom logic.

**Lean toward creating your own when:**

- Your workflow has domain-specific conventions that no generic skill covers — naming patterns, templates, approval flows, brand guidelines.
- You need to chain multiple steps together in a particular order that existing skills don't compose well.
- You've tried an existing skill and find yourself constantly working around it or post-processing its output.
- The "skill" is really encoding your team's decisions — what counts as good output, what gets rejected, what metadata to include. That's institutional knowledge, not generic tooling.
- You want deterministic, repeatable behavior that you can version-control and iterate on.

**The hybrid path (most common in practice):** Start with an existing skill, use it for a few cycles, and notice where it falls short. Then fork or wrap it — keep the core logic but add your own pre/post-processing, templates, or validation.

The maintenance burden is real. A skill you create is a skill you maintain. If the underlying APIs, formats, or models change, you're on the hook.

---

## Custom Email Skill — A Concrete Example

**User:** If I design my email a certain way would I download an internet-based skill that creates emails or create one myself?

**Claude:** Create your own — and it's not close. The moment you said "a certain way," you answered the question. Your email design is a set of your decisions: layout, spacing, tone, brand colors, header structure, CTA placement, how you handle dark mode, what Liquid/merge tags you use, which ESP you're targeting. A generic "email skill" can't encode any of that.

**What goes into the skill:**

- Your HTML boilerplate (table-based layout, inline CSS, responsive breakpoints)
- Your brand tokens — colors, fonts, spacing values, logo placement
- Rules about structure: "hero image goes here, headline max 8 words, body copy max 3 paragraphs"
- Your ESP-specific syntax (Braze Liquid tags, merge fields, conditional blocks)
- What not to do — common mistakes you've caught in review

**What you'd never get from a generic skill:** your specific design system, your compliance/legal footer requirements, your tone and copy conventions, how your templates interact with your ESP's rendering engine.

That said, looking at an existing email skill to understand how it's structured — the file layout, how it instructs the model, how it handles edge cases — is worth doing. Learn from the architecture, then build your own content.

---

## How Claude Uses Skills — Chat vs. Code

**User:** How do you get your agent to use a skill? Is it useful for Claude Chat / Code?

**Claude:**

### Claude Chat (claude.ai)

Skills live on the filesystem at `/mnt/skills/`. Claude is instructed to check them before doing work. The flow:

1. You upload a skill (or Anthropic provides built-in ones) to the skills directory
2. The system prompt tells Claude which skills are available and when to use them
3. When a relevant task comes in, Claude reads the `SKILL.md` file first, then follows its instructions

You don't "call" a skill explicitly. Claude pattern-matches on your request — "make me a PowerPoint" triggers the pptx skill, "create a Word doc" triggers the docx skill.

For a custom email skill, you'd put it in `/mnt/skills/user/email-builder/SKILL.md` and Claude would read it whenever you ask it to build an email. The SKILL.md file is a prompt — it tells Claude how to do the work your way.

### Claude Code (CLI agent)

Similar concept, different mechanism:

- Put a `CLAUDE.md` file in your repo root with instructions and references to skill files
- Point Claude Code at reference templates or style guides in your codebase
- Use the `/read` command to explicitly load a skill file into context

The skill is still just a well-structured instruction document. Claude Code reads it and follows it.

A skill is just a prompt with best practices baked in. Nothing magical — but the structure and specificity make a big difference in output consistency.

---

## False Triggers — When Skills Fire Unintentionally

**User:** Are there instances where Claude will use a skill when you didn't want it to?

**Claude:** Yes, and it's a real friction point. Claude pattern-matches on your request against the skill descriptions in the system prompt. Those descriptions are trigger conditions — and they can be broad.

**Common unwanted trigger scenarios:**

- You ask for a "report" or "summary" meaning a conversational response, but Claude starts generating a `.docx` file
- You mention a file format casually ("I have a .pptx I need to think about") and Claude starts building a presentation
- You ask about how a skill works and Claude interprets that as a request to use the skill
- You're discussing strategy (email strategy, not "build me an email") but the skill triggers anyway

**Mitigation:**

- Be explicit: "don't create a file, just explain it to me"
- For custom skills, write tighter trigger descriptions. Instead of "use when the user mentions emails," write "use ONLY when the user explicitly asks to generate or build email HTML."
- There's no negative trigger system — you can't say "never use this skill when X." You can only make the positive trigger more precise.
- Adding a "when NOT to use this" section in the SKILL.md itself helps Claude distinguish between conversations about the topic and requests to do the work.

---

## Skill Library Scale — Hundreds of Skills

**User:** Are there downsides to having 100s of skills installed? And are they specific to project/session?

**Claude:**

### Performance and context cost

Every installed skill gets listed in the system prompt as an available skill. With hundreds of skills, that list eats into your context window before you've said anything. More skills means:

- **More false triggers** — overlapping descriptions, more chances Claude reads a SKILL.md you didn't want
- **Multiple skill reads burn context** — if your request matches 3-4 skills, it reads all of them
- **Decision quality degrades** — more choices, worse selection; Claude might pick a close-enough skill over the right one

### Scope and persistence

- **Claude Chat:** filesystem resets between conversations. Built-in skills at `/mnt/skills/public/` persist (mounted fresh). Custom skills in `/mnt/skills/user/` are tied to your project — available in every conversation within that project, not across projects.
- **Claude Code:** skills live in your repo or local filesystem. They persist as long as the files exist. Scoped to the repo/directory you're working in.

### Practical advice

Keep the skills relevant to the current project loaded, not every skill you've ever built. Fewer, sharper skills outperform a bloated library every time.

---

## Scoping Skills Per-Project — Chat vs. Code

**User:** How do I keep project-specific skills for Chat vs. Code?

**Claude:**

### Claude Chat

Use Projects. Each project gets its own context:

1. Create a project for a specific domain (e.g., "Email Campaigns")
2. Upload your SKILL.md files as project knowledge
3. Every conversation in that project sees those skills
4. Other projects have different skills entirely

Limitation: uploaded files land in `/mnt/user-data/uploads/`, not a structured skills path. Workaround: name files clearly (e.g., `email-builder-SKILL.md`) and reference them in the project's custom instructions:

> "Before building any email, read the email-builder-SKILL.md file from uploads and follow its instructions."

### Claude Code

Much more natural:

1. Create `.claude/skills/` directory in your repo
2. Drop your SKILL.md files there
3. Reference them in `CLAUDE.md` at the repo root:

```markdown
## Skills
- Before generating email HTML, read `.claude/skills/email-builder/SKILL.md`
- Before writing copy, read `.claude/skills/brand-voice/SKILL.md`
```

Skills travel with the project. Different repo, different skills. And they're version-controlled, so your team shares the same skill definitions.

### Key difference

Claude Code gives proper file structure and version control. Chat gives project isolation but less control over organization. Develop skills in Code, upload the stable version to a Chat project for everyday use.

**Tip for both:** keep a short README or index file listing your skills and their trigger conditions. In Chat, put it in project instructions. In Code, put it in CLAUDE.md. That way Claude has a lookup table instead of guessing.
