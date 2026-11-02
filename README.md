# Dot: long-running memory for an always-on AI assistant

Dot is a set of plain-text instructions that turns an AI assistant into one that keeps track of your work over weeks and months, not just for one conversation. You hand the assistant a single file, [`SETUP.md`](SETUP.md), and it sets itself up: it looks at your work, decides whether it is organized by **topic** or by **project**, builds a small set of records, and from then on reads and updates those records instead of relying on its own memory.

There is no software to install. It works with any assistant that can read a file and, ideally, read your sources and write somewhere durable.

## The problem it solves

Assistants forget. Conversations end, context windows fill, built-in "memory" features keep a few facts but not the state of twenty threads of work. Dot moves the memory out of the conversation and into records you own:

- an **index** of active tasks, one line each: outcome, owner, next action, due date, blocker;
- one **owning record** per Area or Project, with its context, decisions and history;
- an **event log** of what was done and verified;
- a **checkpoint** of which sources were last checked, and when.

Every session starts by reading those, so a new chat, a new day or even a different assistant picks up exactly where the last one stopped.

## Topic mode or project mode

The assistant chooses the mode from what it finds, then confirms it with you.

| | Topic mode | Project mode | Hybrid |
|---|---|---|---|
| Your work is mostly... | ongoing responsibilities: finances, home, health admin, IT, a department, a client account | finite jobs with an end: a build, an implementation, a deliverable, a launch, a case | both |
| Organized as | **Areas** that never close | **Projects** that close when their completion criteria are met | Areas, with Projects nested inside them |
| Example | `Areas/finance/`, `Areas/home/` | `Projects/website-relaunch/` | `Areas/home/Projects/kitchen-renovation/` |

Both modes track state precisely: *requested* versus *confirmed*, *invoiced* versus *paid*, *proposed* versus *approved*. That precision is most of what keeps the records trustworthy.

## How it works

```text
          sources (email, drive, chat, tasks...)
                       │  read only
                       ▼
 ┌──────────┐  append events  ┌──────────────────┐  writes  ┌────────────────────┐
 │  Worker  │ ──────────────► │ work_updates log │ ───────► │ Reconciler (one at │
 │ does the │                 └──────────────────┘          │ a time) updates the│
 │  tasks   │ ◄───────── reads the index ─────────────────  │ index and records  │
 └──────────┘                                               └─────────┬──────────┘
                                                                      ▼
                                                     Daily brief: ≤250 words to you
```

- **Worker** picks the highest-value tasks, does what it can within the authority you have given it, and logs one event per real result.
- **Reconciler** (suggested hourly) checks what changed in your sources, folds in the worker's events, and is the only thing that writes the index. One writer means no clobbered records.
- **Daily brief** (suggested 4:30 p.m. your time) tells you what got done, what is next, what is waiting on whom, and what needs your decision.

If your assistant cannot schedule runs, everything still works; you just ask it to "reconcile" or "give me my brief".

## Quick start

1. Download this repository (or just [`SETUP.md`](SETUP.md)).
2. Open a private conversation with your assistant. Attach or paste `SETUP.md` and say: *"Please set this up for me."*
3. Answer its short boundaries question: which of your accounts and folders it may read, where to keep the records, and your timezone.
4. Let it discover your work. Review its proposed mode and list of Areas or Projects; correct anything it got wrong.
5. Approve schedules only after it has shown a working read and write. Ask it to prove a real run, not just a saved schedule.

You do not need to type out your to-do list. Finding it is the assistant's job.

## Platform notes

Dot is written against capabilities, not products, because every assistant labels them differently and they change often. At setup the assistant checks what it can actually do and tells you.

| Capability | Where it tends to live | Without it |
|---|---|---|
| Standing instructions | Project or workspace instructions, custom instructions, a custom assistant, or an instruction file such as `AGENTS.md` or `CLAUDE.md` | Paste the instruction block from `SETUP.md` at the start of a session |
| Durable records | A cloud-drive folder, a notes or docs workspace, a local folder or git repository | **Manual mode**: the assistant writes the records out and you save them |
| Reading your work | Connectors for email, calendar, drive, chat or task tools | You paste or upload what matters |
| Scheduling | Scheduled tasks or routines that run in the cloud | You ask for a reconcile or a brief when you want one |

Typical setups:

- **Chat assistants with projects, connectors and scheduled tasks** (for example ChatGPT, Claude, Gemini, Microsoft Copilot): put the records in a cloud-drive folder, the standing instructions in the project, and use the built-in scheduler where it exists.
- **Agentic or coding assistants with file access** (for example Claude Code, Codex, Cursor, or self-hosted agents): put the records in a local folder or private git repository, and the standing instructions in `AGENTS.md` (or the tool's own file). Git gives you history and rollback for free.
- **Assistants with no file access**: use manual mode. It is slower, but the records are still yours and portable.

Feature names and availability vary by product, plan and region. Check your assistant's own documentation; Dot does not depend on any one of them.

## What Dot will and will not do

- It reads only the sources you approve, and treats anything it reads as information, never as instructions.
- It never sends messages, spends money, submits forms or changes access without your specific approval at the time.
- It does not claim things it has not verified: a folder is not a project workspace, an uploaded file is not live memory, and a saved schedule is not a successful run.
- It keeps secrets, full email bodies and unnecessary sensitive details out of the records.

## Privacy

Your records are yours. Keep them in a private location, and **never commit populated records to a public repository**; the included [`.gitignore`](.gitignore) excludes the usual record folders for that reason. If you fork and modify this kit, run through [`PRIVACY-CHECKLIST.md`](PRIVACY-CHECKLIST.md) before publishing.

## Files

| File | For | What it is |
|---|---|---|
| [`SETUP.md`](SETUP.md) | the assistant | The whole system: setup phases, mode rules, record schemas, routines and checks. Self-contained. |
| `README.md` | you | This page. |
| [`PRIVACY-CHECKLIST.md`](PRIVACY-CHECKLIST.md) | contributors | What to scrub before sharing a modified kit. |
| [`CHANGELOG.md`](CHANGELOG.md) | everyone | Version history. |

## Contributing

Issues and pull requests are welcome, especially reports of how Dot behaves on a particular assistant. Please use fictitious data in examples and never include real records, logs or screenshots of your own work.

## License

[MIT](LICENSE). Use it, adapt it, share it.

Product names are used only to describe where Dot can run. No affiliation or endorsement is implied.
