# REA-API

A Claude Code skill that turns a click path in a web app into a command.

A web app's buttons only send requests. The skill finds those requests and calls them directly from your logged-in
browser session, so a path you click more than once no longer needs clicks.

The whole skill is one file: [`SKILL.md`](SKILL.md). It carries no site-specific knowledge. The model works out each
site on its own; the file only holds what does not depend on the site.

## Install

```sh
git clone https://github.com/Luis247911/REA-API.git ~/.claude/skills/api-rea
```

Nothing to build, no dependencies. You need one tool that sees the network of your real browser: browser-harness,
Chrome DevTools MCP or Claude in Chrome. Without one, the skill asks you for a HAR export of one run.

## Use

Open Claude Code in the project that clicks today and say what should stop needing clicks:

```
/api-rea publishing a listing in <app> should be one command
```

The trigger phrases in the `description` are German ("API REA", "klickfrei machen", "interne API herausfinden").
Edit them if you work in another language.

## What you get

- One command per click path, inside your project, using what the project already uses.
- A short note beside it: the calls, where each changing value comes from, the date, how to find them again when the
  site changes.
- A report: path, command, seconds before and after, what stays manual and why.

## Scope

Only for accounts and systems you own or may operate. If the app has an official API, export or connector for the
same thing, the skill uses that and stops.

## History

Until November 2026 this repository held `reverse-engineering-kit`, a TypeScript pipeline that generated typed API
clients from captured traffic. It is still in the Git history; the last commit with the kit is `d758b54`.

## License

MIT, see [`LICENSE`](LICENSE).
