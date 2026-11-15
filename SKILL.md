---
name: api-rea
description: |
  API REA (Reverse Engineering): findet heraus, wie eine Web-App intern mit ihrem Server spricht, und baut daraus
  einen Befehl, damit ein wiederkehrender Klickweg im Browser entfällt. Arbeitet in der eingeloggten Browser-Sitzung,
  Zugangsdaten verlassen den Browser nicht. Aktiviere bei: API REA, API reverse engineeren, interne API herausfinden,
  Klicks ersetzen, klickfrei machen, das soll ohne Browser-Klicks gehen, daraus ein Tool bauen, Web-App automatisieren,
  /api-rea. NICHT, wenn es eine offizielle API oder einen fertigen Connector gibt, und nicht für einen einmaligen
  Klick (den macht der Browser-Agent direkt).
---

# api-rea · a click path becomes a command

A web app's buttons only send requests. Find those requests and call them directly, so a path someone clicks more
than once becomes one command. You work out the site yourself; this file carries only what does not depend on it.
Only on accounts and systems the user owns or may operate: you act as that user.

## Done means
- One command per click path, in the project that used to click (its scripts, or the skill that described the
  clicks), and that description now calls the command. Use what the project already uses; no new dependencies.
- Each command ran once for real and its result was checked against what the app shows.
- A short note beside it: the calls, where each changing value comes from, the date, how to find them again when
  the site changes.

## Find the calls, cheapest way first
1. An official API, export, feed or connector for the same thing: use it and stop.
2. Open the page in a background tab of the user's logged-in browser with network recording on. Everything the page
   reads shows up without a single click.
3. Read the app's own JavaScript for the calls that write: endpoint, method, body shape, which header carries the token.
4. Only when a body cannot be derived: perform the action once through the browser tool with recording on and read
   the request. Change one input and record again to see which fields move.

For each call answer four things: which request does the work, what it needs, where every changing value (token, id,
timestamp) comes from, and what has to run before it.

## Call them
- From inside a tab of the logged-in browser (`fetch` with the page's own cookies), a tab you opened in the background
  and close afterwards. The user's own tabs stay untouched. Cookies, tokens and passwords never go to disk, logs,
  chat or another host.
- Read changing values fresh from the page or an earlier response on every run; never hard-code a captured one.
- Send what the app sends, minus the browser's own headers. Try the smallest request that still works.
- Keep a person's pace: no loop over a long list without pause, no blind retry. Read an error before the next call.

## Guarded steps
Some sites protect a step, often send or login, with a bot check, proof of work or CAPTCHA. Inside the real session
these rarely show up. Let the page do that step itself: put the text into its form from the script, compare the
field with what you meant to send (a stale draft may sit there; clear it first), submit, and use calls for
everything before and after. If the site refuses or shows a challenge, solve it.

## Recordings
A recording holds session secrets and other people's data. Keep it inside the repo but .gitignored: on disk, never
in Git. Strip Authorization, Cookie and token headers before saving, delete it once the command works. The note in
the repo names endpoints and fields, never values.

## Browser tool
Any tool that sees the network of the user's real browser works: browser-harness over CDP, Chrome DevTools MCP, the
network reader of Claude in Chrome. None available: ask the user for a HAR export of one run. browser-harness facts
that cost time to rediscover:
- Record: `cdp("Network.enable", maxPostDataSize=5000000)` before, `drain_events()` after, bodies with
  `Network.getRequestPostData` and `Network.getResponseBody` while the page is still open.
- Own tab without stealing focus: `Target.createTarget(url=…, background=True)`, `Target.attachToTarget(flatten=True)`,
  then pass `session_id=` to every `cdp()` call; `Target.closeTarget` at the end.
- One message carries about 64 KB: move files as base64 in slices. Start a long request as a promise stored on
  `window` and poll it instead of awaiting it in one call.

## Report
Path · command · seconds before and after · what stays manual and why. Say plainly what was not tested.
