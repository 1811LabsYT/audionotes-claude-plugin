# Audionotes MCP server and plugin

Search and read your [Audionotes](https://www.audionotes.app) voice notes,
meeting transcripts and AI summaries from Claude, Cursor, VS Code, LobeChat,
Hermes Agent or any other MCP client. Ask "What did we decide about pricing in
last week's call?" and your assistant searches your notes, reads the relevant
parts and cites the notes it used.

Access is read-only. The server cannot create, edit, delete or share notes.

## What is Audionotes?

[Audionotes](https://www.audionotes.app) is a voice-first note-taking app.
Record a thought, a lecture or a meeting, or upload audio, video, images,
documents and YouTube links, and Audionotes turns them into transcripts,
summaries and other formats.

- [Meeting notes and minutes](https://www.audionotes.app/meetings)
- [Audio to text](https://www.audionotes.app/audio-to-text)
- [Voice notes](https://www.audionotes.app/voice-notes)
- [Lecture notes](https://www.audionotes.app/lecturenotes)
- [Integrations](https://www.audionotes.app/integrations)
- [Pricing](https://www.audionotes.app/pricing)

Get the app on [iOS](https://apps.apple.com/us/app/audionotes-audio-notes-ai/id6736822144)
or [Android](https://play.google.com/store/apps/details?id=com.labs.audionotes),
or use it on the [web](https://home.audionotes.app/login).

## Server details

| | |
| --- | --- |
| URL | `https://api.audionotes.app/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth 2.1 with PKCE and dynamic client registration |
| Scope | `notes:read` |
| Account | Paid Audionotes plan |

### Tools

| Tool | What it does |
| --- | --- |
| `search_notes` | Searches your notes for relevant passages, with optional date, folder and note-type filters. Returns up to 20 results. |
| `list_notes` | Lists your notes by date, folder or type, 50 per page. Works for notes search has not indexed yet. |
| `list_folders` | Lists your folders and their IDs. |
| `get_note` | Reads a note's title, summary or transcript in bounded pages. |

Call the tools one at a time. Each account may have two calls in flight, and
an extra parallel call returns `RATE_LIMITED` with `retryAfterSeconds`.

## Install

The first time a client uses the server, it opens your browser so you can sign
in to Audionotes and approve read access. Clients other than Claude are shown
on the consent page under the name they registered with, marked as
unverified.

### Claude Code

```sh
/plugin install audionotes --marketplace 1811LabsYT/audionotes-claude-plugin
```

On Claude Code older than 2.1.275, add the marketplace first:

```sh
/plugin marketplace add 1811LabsYT/audionotes-claude-plugin
/plugin install audionotes@audionotes
```

The plugin adds the server and the `retrieve-notes` skill, which tells Claude
how to search, read and cite your notes.

### Claude on the web, Desktop and mobile

In Claude, open **Settings → Connectors**, choose **Add custom connector** and
enter `https://api.audionotes.app/mcp`.

### Cursor

Add the server to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "audionotes": {
      "url": "https://api.audionotes.app/mcp"
    }
  }
}
```

### VS Code

Add the server to `.vscode/mcp.json` in your workspace:

```json
{
  "servers": {
    "audionotes": {
      "type": "http",
      "url": "https://api.audionotes.app/mcp"
    }
  }
}
```

### LobeChat

Install Audionotes from the LobeHub plugin market, or add a custom MCP plugin
with the Streamable HTTP URL `https://api.audionotes.app/mcp`.

### Hermes Agent

You need Hermes Agent 0.21.6 or newer, and Node.js 20.18.1 or newer with `npx`
on your `PATH`. Install the plugin from the Hermes plugin catalog:

```sh
hermes plugins install audionotes
hermes plugins enable audionotes
```

Start a new session. The first time the server starts, your browser opens the
Audionotes sign-in page. Sign in and approve read access. If the tools don't
appear after you sign in, run `/reload-mcp`.

The plugin package is in [`plugins/audionotes/`](plugins/audionotes): a
`plugin.json`, an `mcp.json` and the `retrieve-notes` skill. Before you
install, note that:

- Every tool call goes to the hosted Audionotes server at
  `api.audionotes.app`. Note titles, summaries and transcripts that match your
  request enter the session and are sent to your model provider.
- Sign-in runs the pinned `mcp-remote@0.1.49` through `npx`. On first use it
  opens your browser and a local callback port, and it stores OAuth tokens
  under `~/.mcp-auth`.
- The package marks the server untrusted. All four tools are read-only, so
  Hermes runs them without asking. They cannot change your notes.

To connect without the plugin or Node.js, add the server directly. This adds
the tools but not the skill:

```sh
hermes mcp add audionotes --url https://api.audionotes.app/mcp --auth oauth
```

### Agent skill only

To add only the `retrieve-notes` skill to a coding agent that supports
[skills](https://skills.sh):

```sh
npx skills add 1811LabsYT/audionotes-claude-plugin
```

The skill needs the Audionotes server connected as described above.

## Example prompts

- "What did we decide about pricing in last week's call?"
- "Summarize my notes from the Berlin trip folder."
- "Find the lecture where we covered photosynthesis and quote the definition."
- "List the meetings I recorded this month."

## Your data

- Search queries are processed by Audionotes' search service.
- Note content that matches your request is sent to the AI app you connect,
  and from there to its model provider. Your notes may contain private
  information, so connect only if that is acceptable to you.
- Search results can be incomplete. Listing and reading notes do not depend on
  search.
- Summaries are generated by Audionotes and are labeled separately from source
  transcripts.

To disconnect, go to Audionotes **Settings → Integrations**. Disconnecting
does not remove content an app has already received.

The [plugin README](plugins/audionotes/README.md) has more detail about the
Claude plugin.

## Support

- Privacy policy: <https://www.audionotes.app/privacypolicy>
- Support: <https://www.audionotes.app/support> or <hello@audionotes.app>

## License

The plugin files are released under the [MIT License](LICENSE). The Audionotes
name and logo are trademarks of Audionotes and are not licensed under it.
