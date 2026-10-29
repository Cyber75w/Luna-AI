# LUNA AI

LUNA AI is a local-first terminal chat app powered by Ollama. It opens to a ChatGPT-style welcome screen with a centered prompt box and a sidebar for recent chats and projects. Starting a prompt opens a conversation; chats and projects are stored locally. The interface adapts to narrower terminals. The default model is `qwen2.5-coder:1.5b`.

## Install (Linux / macOS, including fish)

From the LUNA AI project folder:

```sh
python3 -m venv .venv
./.venv/bin/python -m pip install -e .
ollama pull qwen2.5-coder:1.5b
./install_luna
```

Open a new fish terminal and type `luna`. The installer adds a fish function that works from any directory. To use it in the current fish session, run the `source .../luna-ai.fish` command printed by the installer. To launch without installing the fish command, use `./run_luna` from the project folder.

For system clipboard integration on Linux, install `wl-clipboard` (Wayland) or `xclip`/`xsel` (X11). Luna also keeps copied text in its own clipboard while running.

Ollama Desktop may already run the local service. If LUNA AI reports Ollama as unreachable, start Ollama or run `ollama serve` in another terminal.

## Windows

```powershell
py -m venv .venv
.venv\Scripts\python -m pip install -e .
ollama pull qwen2.5-coder:1.5b
.venv\Scripts\python -m luna
```

## Controls

- Enter sends a message; Ctrl+A selects all text in the focused input; Ctrl+C copies selected text to the system clipboard; Ctrl+V pastes from it. Ctrl+P / Ctrl+N browse prompt history; Ctrl+R retries the last reply; Escape stops generation; Ctrl+Q quits.
- Type `/` for command suggestions; Tab completes a command.
- Ctrl+T cycles dark, light, and high-contrast themes; Ctrl+E exports the chat.
- `/new`, `/chats`, `/open <number or id>`, `/rename <title>`, and `/delete <number or id>` manage saved conversations. Sidebar search matches chat titles and message text; `/search <text>` searches titles and messages, `/search here <text>` limits it to the active chat, and `/search open <number>` opens and jumps to a result.
- Use **New chat** and **New project** in the sidebar to organize work. Select the **⋯** beside any chat to rename it, move it to a project, or delete it; use **⋯** beside a project to rename or delete it. Deleting a project keeps its chats under All chats. The matching commands are `/project create <name>`, `/project use <name>`, `/project all`, and `/project list`.
- `/edit` loads the last prompt for editing and resending; `/retry` regenerates its reply. `/length <64–8192>` sets the response limit; `/temperature <0–2>` adjusts response variety.
- Include local text files with `@path/to/file` in a prompt. Quote paths with spaces, e.g. `@"meeting notes.txt"`. LUNA attaches up to four files and 40 KB of text per prompt.
- `/pick` opens a file browser with previews. Text and PDF context is extracted locally; images are sent only to Ollama models that advertise vision support. Install optional PDF support with `./.venv/bin/python -m pip install -e '.[pdf]'`.
- `/index add <folder-or-file>` adds to the local document library; `/index update <path>`, `/index remove <path>`, and `/index list` maintain sources; `/knowledge <query>` searches it. Refreshing a file or folder replaces its old passages, including stale passages when a source can no longer be read. Text sources cite line ranges and PDFs cite page numbers. Indexing skips common dependency folders and is capped to keep it small.
- Search combines local keyword scoring with cached Ollama embeddings when available. To enable semantic retrieval, install an embedding model with `ollama pull nomic-embed-text`, then re-index sources. `/index list` marks files that changed or disappeared; refresh one with `/index update <path>`. Retrieved citations are listed with locations; `/source 1` opens the most recently retrieved source.
- `/template` lists reusable prompts. Use `/template use <name>`, `/template save <name> | <prompt>`, or `/template delete <name>`.
- `/branch [turn] [title]` creates an independent copy of the chat up to a user turn. `/pin` pins or unpins; `/archive` toggles archive status. Use **Archived chats** in the sidebar to browse archived conversations.
- Project menus let you set project-specific instructions; Luna applies them to chats in that project. Use `/project files add <path>` while in a project to attach and index files for project chats, and `/project files list|remove <path>` to manage them. Relevant project sources are prioritized and replies include source paths with page or line locations.
- `/note add <text>`, `/note list`, and `/note delete <number>` save editable reference notes to the selected project.
- Use **Select chats** in the sidebar, tick several chats, then run `/bulk archive selected`, `/bulk pin selected`, `/bulk delete selected`, or `/bulk move selected <project>`. You can also pass comma-separated chat IDs instead of `selected`.
- `/tag add <name>` and `/tag remove <name>` label a chat; filter the sidebar with `tag:name`. **Continue recent** opens the latest conversation with messages.
- Long conversations are summarized locally when context grows; use `/compact` to summarize sooner and `/memory show|set <text>|clear` to review or edit the saved summary. Full transcripts remain in local chat history.
- `/model <name>` changes the model for the current chat. Settings controls the default for new chats. Regenerating a reply keeps up to five earlier versions; use the **Versions** button or `/versions` to compare them.
- Choose a folder with `/workspace set <folder>`. `/file read <path>` displays a local file; `/file ask <path> | <question>` asks Luna about it; `/file edit <path> | <old text> => <new text>` previews a diff before applying it. `/run <command>` shows the command and working directory for approval, then runs it without a shell. Workspace file edits and commands stay inside the selected folder.
- `/profile save <name>` saves the current model, reply limit, temperature, and system prompt. Use `/profile use <name>`, `/profile list`, and `/profile delete <name>` to manage profiles.
- **Settings → Keyboard shortcuts** adds optional Ctrl+Shift+letter shortcuts for theme, export, regenerate, stop, prompt history, and quit. Defaults remain available; `/help` lists the keyboard guide.
- `/compare model-a,model-b | prompt` runs one prompt against two installed Ollama models and shows both answers. `/stats` reports the last generation speed and token count.
- `/settings` lets you choose an installed Ollama model (or enter a model tag), set a persistent system prompt describing how Luna should respond, and edit reply limit, temperature, theme, and chat retention. Set retention to `0` to keep all chats; otherwise older chats are removed on save, with the active chat retained. `/stats` shows generation speed, the model loaded in Ollama, and system RAM; install optional system metrics with `./.venv/bin/python -m pip install -e '.[system]'`. Stop generation with Escape or the Stop button; regenerate with Ctrl+R or a reply's Regenerate button. `/backup` creates a portable ZIP of chats, project file links, index, and settings; `/restore "/path/to/luna-backup.zip"` imports one.
- `/voice` or the 🎙 button records up to eight seconds and transcribes locally. **Speak** plays the last response with optional offline TTS. Install optional support with `./.venv/bin/python -m pip install -e '.[voice]'`, then install a Vosk language model separately and set `LUNA_VOSK_MODEL` to its folder (or place it at `~/.local/share/luna/vosk-model`). Luna inserts transcripts for review and does not send audio online.
- On first launch, **Check local setup** guides you through Ollama availability and model installation; the same diagnostics are available at `/setup`.
- `/data status` shows local data usage; `/data clear project <name>` removes that project's chats, linked files, and indexed passages; `/data clear all` removes chats, projects, project file links, and the document index while keeping settings and prompt templates. Settings' chat-retention limit automatically prunes older chats on save.
- Replies render Markdown with syntax-highlighted code. `/copy` copies the last reply; `/copycode [number]` copies code blocks; `/savecode [filename]` saves code locally; `/export` writes a Markdown transcript.
- Export a chat as Markdown, standalone HTML, or JSON with `/export md`, `/export html`, or `/export json`. Exported files are readable plaintext, even when the local data vault is encrypted.
- Bookmark any assistant answer with its **Bookmark** action; use `/bookmark [message-number] [label]` for a specific message, `/bookmarks` to list saved points, and `/jump <bookmark-number>` to scroll back.
- `/cite` previews excerpts saved with the latest cited answer. Choose an excerpt to open its source; supported PDF viewers can open to the cited page, and `$EDITOR` opens text sources at their cited line.
- Offline OCR adds scanned PDFs and image text to the local index. Install the Python support with `./.venv/bin/python -m pip install -e '.[ocr]'` and install Tesseract plus its language data through your operating system. No OCR service or model is contacted. Re-index a changed file with `/index update <path>`.
- Compare saved prompts with `/bench save <name> | <prompt>`, then `/bench run [model-a,model-b]`. Without model names, Luna runs the benchmark against installed models (up to 5 models and 8 prompts per run) and records elapsed time and answers locally. Browse saved runs with `/bench results` or `/bench results 1` for the latest.
- Reusable prompt chains: `/workflow save polish | summarize -> critique -> rewrite`, `/workflow list`, and `/workflow run polish | <input>`. A step can use `{input}` to mark where the previous result goes.
- `/lock` enables optional encryption for Luna's local JSON data. Install with `./.venv/bin/python -m pip install -e '.[secure]'`; Luna asks for the passphrase at startup. The passphrase cannot be recovered. Exports and code snippets you save outside the data store remain plaintext. Encrypted ZIP backups include the vault metadata; to restore one on another computer, extract its `lock.json` into that computer's Luna data folder before starting Luna, then unlock with the same passphrase and import the ZIP.
- `/display auto|symbols|text` selects image display behavior. `/image <path>` uses Chafa symbols when available; auto mode falls back to image metadata, symbols mode requires Chafa, and text mode always shows metadata only. Markdown tables in assistant replies use Rich's terminal table rendering.
- Each Luna reply has **Copy response** and **Rate response** actions. Positive ratings save a few local style examples; negative ratings save optional guidance that is included in future model prompts. This feedback stays in `style_feedback.json` in the local data folder.
- `/model [name]` changes the active chat's model; `/models` and `/setup` manage and diagnose Ollama. `/theme [dark|light|contrast]`, `/density`, and `/accessibility` adjust display options.
- `/help`, `/about`, `/clear`, `/credits`, and `/quit` provide help, app details, chat clearing, credits, and exit.

Chats, bookmarks, model choice, response length, theme, spacing, templates, profiles, shortcuts, workflows, benchmark prompts/results, projects, project file links, style feedback, and the document index are saved locally. On Linux, LUNA AI stores them under `~/.local/share/luna/` (`session.json`, `projects.json`, `project_files.json`, `templates.json`, `profiles.json`, `shortcuts.json`, `knowledge.json`, `benchmarks.json`, `workflows.json`, `exports/`, `snippets/`, and `backups/`). Set `LUNA_DATA_DIR` to change the data folder, `LUNA_MODEL` to override the model, or `OLLAMA_URL` for a different Ollama address. Use your terminal's zoom controls to change text size.
