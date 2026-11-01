# Audionotes plugin for Claude

This repository holds the Audionotes plugin for Claude. The plugin lets Claude
search and read your Audionotes recordings, transcripts and summaries. It is
read-only.

The plugin is in [`plugins/audionotes`](plugins/audionotes). Its
[README](plugins/audionotes/README.md) explains what it does, what data it
sends and how to disconnect it.

## Install in Claude Code

```sh
/plugin install audionotes --marketplace 1811LabsYT/audionotes-claude-plugin
```

On Claude Code older than 2.1.275, add the marketplace first:

```sh
/plugin marketplace add 1811LabsYT/audionotes-claude-plugin
/plugin install audionotes@audionotes
```

You need a paid Audionotes account. Claude asks you to sign in to Audionotes
the first time it uses the plugin.

## Support

- Privacy policy: <https://www.audionotes.app/privacypolicy>
- Support: <https://www.audionotes.app/support> or <hello@audionotes.app>

## License

The plugin files are released under the [MIT License](LICENSE). The Audionotes
name and logo are trademarks of Audionotes and are not licensed under it.
