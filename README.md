# novash

---

Production-ready Unix-like shell written in Go, focused on correctness, OS-level behavior, and custom parsing logic.

---

## Project Overview

**novash** is a Unix-like shell implemented from scratch in Go.

The goal of this project is to deeply understand how real shells work internally — from lexical analysis and parsing to process execution, signal handling, redirection, and environment variable expansion.

Rather than relying on existing shell libraries, novash implements:

- A custom lexer and parser
- Built-in command handling
- External process execution
- Output redirection (stdout/stderr)
- Environment variable expansion
- Signal handling (Ctrl+C)
- Multi-line command parsing

This project emphasizes correctness, low-level behavior, and clean architecture over shortcuts.

---

## Key Features

### Core Shell Behavior

- **Custom Command Parser** – Full lexer with support for:
  - Single quotes `'`
  - Double quotes `"`
  - Escape characters `\`
  - Multi-line commands
- **Environment Variable Expansion** – `$VAR` resolution from OS environment
- **Command Resolution** – Searches system `PATH` using `exec.LookPath`
- **Exit Code Tracking** – Maintains `lastExitCode` similar to real shells

---

### Built-in Commands

- `exit` – Terminates the shell
- `echo` – Prints arguments
- `type` – Identifies built-in vs external commands
- `pwd` – Prints current working directory
- `cd` – Changes directory (supports `~` expansion)

Built-ins are implemented separately from external commands, mirroring real shell architecture.

---

### Process Execution

- Executes external programs using `os/exec`
- Context-based cancellation for interrupt handling
- Proper exit code propagation
- Full stdin/stdout/stderr wiring

---

### Redirection Support

Supports:

- `>` / `1>` – Redirect stdout
- `2>` – Redirect stderr
- `&>` – Redirect stdout and stderr
- `>>` / `1>>` – Append stdout
- `2>>` – Append stderr
- `&>>` – Append both

Redirection works for both built-in and external commands.

---

### Signal Handling

- Captures `Ctrl+C` (`os.Interrupt`)
- Cancels running processes using `context.WithCancel`
- Properly resets prompt state after interruption
- Prevents shell termination when interrupting child processes

---

## Technologies Used

| Layer        | Technology |
|--------------|------------|
| Language     | Go (Golang) |
| Concurrency  | Goroutines + Channels |
| Process Exec | os/exec |
| Signals      | os/signal |
| Parsing      | Custom lexer implementation |
| Environment  | OS-level variables |

---

## What I Learned

This project significantly deepened my understanding of:

- How real Unix shells tokenize input
- Quoting and escaping edge cases
- Process lifecycle management
- Signal handling and context cancellation
- File descriptor manipulation
- Stdout/Stderr redirection mechanics
- PATH resolution and executable lookup
- Environment variable parsing rules
- Writing stateful lexers from scratch

Most importantly, I learned how much subtle complexity exists behind something as “simple” as a shell.

---

## Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Proper quote handling | Implemented state-based lexer (`in_single_quotes`, `in_double_quotes`, `escape_next`) |
| Multi-line input detection | Parser tracks unfinished quote/escape states |
| Ctrl+C without killing shell | Used `context.WithCancel` and signal forwarding |
| Built-in vs external separation | Command dispatch map for built-ins |
| Redirection for built-ins | Temporarily reassigned `os.Stdout` / `os.Stderr` |

---

## Cool & Special Features

- Fully custom lexer (no external parsing libraries)
- Multi-line command continuation with prompt switching
- Correct literal handling inside single quotes
- Context-aware process cancellation
- Built-in + external command architecture separation
- Clean command dispatch via map-based lookup
- Append vs overwrite redirection logic
- Smart `cd` Behavior — Automatically lists directory contents after changing directories.  

---

## Architecture Overview

```text
+--------------------+
|      Terminal      |
+--------------------+
          |
          v
+--------------------+
|   Input Reader     |  (bufio + goroutine)
+--------------------+
          |
          v
+--------------------+
|   Lexer (Tokenizer)|
|  - Quotes          |
|  - Escaping        |
|  - State tracking  |
+--------------------+
          |
          v
+--------------------+
|   Parser           |
|  - Command split   |
|  - Var expansion   |
+--------------------+
          |
          v
+--------------------+
| Command Dispatcher |
| Built-in | External|
+--------------------+
        |         |
        v         v
+------------+  +----------------+
| Built-ins  |  | os/exec Cmd    |
+------------+  +----------------+
        |                |
        v                v
   File Redirection   OS Process
```

---

## Installation & Usage

### 1. Clone the Repository

```bash
git clone https://github.com/bozhanov21/novash.git
cd novash
```

---

### 2. Build the Shell

```bash
go build -o novash
```

This will generate a `novash` executable in the project directory.

---

### 3. Run novash

```bash
./novash
```

You should now see the shell prompt:

```
$
```

You are now inside **novash**.

---

## Basic Usage

Run commands just like in a normal Unix shell:

```bash
$ echo Hello
Hello

$ pwd

$ cd ~

$ ls > output.txt
```

---

## Exit novash

To exit the shell:

```bash
$ exit
```

You will return to your original system shell (bash, zsh, etc.).

---

## Set novash as Your Default Shell (Optional)

⚠️ Only do this if you are comfortable changing your login shell.

### Step 1: Move the Binary to a Standard Location

```bash
sudo mv novash /usr/local/bin/novash
```

---

### Step 3: Change Your Default Shell

```bash
chsh -s /usr/local/bin/novash
```

Log out and log back in — novash will now start automatically.

---

## Revert Back to Your Original Shell

If you want to switch back (for example to bash):

```bash
chsh -s /bin/bash
```

Or for zsh:

```bash
chsh -s /bin/zsh
```

Log out and back in to apply the change.

---

## Notes

- novash is a learning-oriented Unix-like shell and does not yet implement full POSIX compliance.
- Some advanced shell features (pipes, job control) are planned for future versions.
- It is recommended to test novash interactively before setting it as your default login shell.

---

## Design Goals

- Correct Unix-like behavior
- Clean separation of concerns
- Minimal external dependencies
- OS-level realism
- Testable architecture
- Explicit state handling instead of hidden magic

---

## Future Improvements

- Pipe support (`|`)
- Job control (`fg`, `bg`)
- Command history persistence
- Auto-completion
- Config file support (`.novashrc`)
- Improved error messaging
- Unit tests for lexer edge cases
- POSIX compliance improvements

---

## Why This Project Matters

Writing a shell forces you to understand:

- Operating system fundamentals
- Process spawning and lifecycle
- File descriptor manipulation
- Signal propagation
- Parsing complexity in real-world software

---

## License

This project is open-source and free to use, modify, and learn from.

---

## Contribution

Contributions are welcome! Please open an issue or submit a pull request.
