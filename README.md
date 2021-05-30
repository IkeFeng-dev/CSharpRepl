# C# REPL

C# REPL is a tool to promote rapid experimentation and exploration of C# expressions, statements, and nuget packages. It provides a command line <a href="https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop" target="_blank"><abbr title="Read Eval Print Loop">REPL</abbr></a> for C#, supporting intellisense, documentation, and nuget package installation.

<p align="center">
  <img src="https://raw.githubusercontent.com/waf/CSharpRepl/main/.github/readme_assets/csharprepl.png" alt="C# REPL screenshot" style="max-width:80%;">
</p>

C# REPL helps you explore how libraries work, both in the standard library and in nuget packages, as well as iteratively defining and refining functions when developing software.

## Installation

C# REPL is a .NET 5 global tool. It can be installed via:

```console
dotnet tool install -g csharprepl
```

And then run by executing `csharprepl` at the command line.

## Usage:

Run `csharprepl` from the command line to start. The default colorscheme uses the color palette defined by your terminal, but these colors can be changed using a [`theme.json`](https://github.com/waf/CSharpRepl/blob/main/CSharpRepl/themes/dracula.json) file provided as a command line argument.

### Evaluating Code

Type some C# into the prompt and press <kbd>Enter</kbd> to run it. The result, if any, will be printed:

```csharp
> Console.WriteLine("Hello World")
Hello World

> DateTime.Now.AddDays(8)
[6/7/2021 5:13:00 PM]
```

To evaluate multiple lines of code, use <kbd>Shift+Enter</kbd> to insert a newline:

```csharp
> var x = 5;
  var y = 8;
  x * y
40
```

Additionally, if the statement is not a "complete statement" a newline will automatically be inserted when <kbd>Enter</kbd> is pressed. For example, in the below code, the first line is not a syntactically complete statement, so when we press enter we'll go down to a new line:

```csharp
> if (x == 5)
  | // caret position, after we press Enter on Line 1
```

Pressing <kbd>Ctrl+Enter</kbd> will evaluate the current code, but show a "detailed view" of the response. For example, for the `DateTime.Now` expression below, on the first line we pressed <kbd>Enter</kbd>, and on the second line we pressed <kbd>Ctrl+Enter</kbd> to view more detailed output:

```csharp
> DateTime.Now // Pressing Enter shows a reasonable representation
[5/30/2021 5:13:00 PM]
> DateTime.Now // Pressing Ctrl+Enter shows a detailed representation
[5/30/2021 5:13:00 PM] {
  Date: [5/30/2021 12:00:00 AM],
  Day: 30,
  DayOfWeek: Sunday,
  DayOfYear: 150,
  Hour: 17,
  InternalKind: 9223372036854775808,
  InternalTicks: 637579915804530992,
  Kind: Local,
  Millisecond: 453,
  Minute: 13,
  Month: 5,
  Second: 0,
  Ticks: 637579915804530992,
  TimeOfDay: [17:13:00.4530992],
  Year: 2021,
  _dateData: 9860951952659306800
}
```

**A note on semicolons**: C# expressions do not require semicolons, but [statements](https://stackoverflow.com/questions/19132/expression-versus-statement) do. If a statement is missing a required semicolon, a newline will be added instead of trying to run the syntatically incomplete statement; simply type the semicolon to complete the statement.

```csharp
> var now = DateTime.Now; // assignment statement, semicolon required

> DateTime.Now.AddDays(8) // expression, we don't need a semicolon
[6/7/2021 5:03:05 PM]
```

### Exploring Code

- Press <kbd>F1</kbd> when your caret is in a type, method, or property to open its official MSDN documentation.
- Press <kbd>Ctrl+F1</kbd> to view its source code on https://source.dot.net/.
- Press <kbd>Ctrl+Shift+C</kbd> to copy the current line of code to your clipboard, and <kbd>Ctrl+v</kbd> to paste.

For example, pressing <kbd>F1</kbd> or <kbd>Ctrl+F1</kbd> when the caret is in the `AddDays` function will open 
the [MSDN documentation for `AddDays`](https://docs.microsoft.com/en-US/dotnet/api/System.DateTime.AddDays?view=net-5.0) and the [source code for `AddDays`](https://source.dot.net/#q=System.DateTime.AddDays) respectively.

```csharp
> DateTime.Now.AddD|ays(8) // "|" denotes caret position
```

### Adding References

Use the `#r` command to add assembly or nuget references.

- For assembly references, run `#r "AssemblyName"` or `#r "path/to/assembly.dll"`
- For nuget references, run `#r "nuget: PackageName"` to install the latest version of a package, or `#r "nuget: PackageName, 13.0.5"` to install a specific version (13.0.5 in this case).

<p align="center">
  <img src="https://raw.githubusercontent.com/waf/CSharpRepl/main/.github/readme_assets/nuget.png" alt="Installing nuget packages" style="max-width:80%;">
</p>

## Command Line Configuration

The C# REPL supports multiple configuration flags to control startup, behavior, and appearance:

```
Usage: csharprepl [OPTIONS] [response-file.rsp] [script-file.csx]
```

Supported options are:

- OPTIONS:
    - `-r <dll>` or `--reference <dll>`: Add an assembly reference. May be specified multiple times.
    - `-u <namespace>` or `--using <namespace>`: Add a using statement. May be specified multiple times.
    - `-f <framework>` or `--framework <framework>`: Reference a [shared framework](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/metapackage-app). Available shared frameworks depends on the local .NET installation, and can be useful when running a ASP.NET application from the REPL. Example frameworks are, but not limited to:
        - Microsoft.NETCore.App (default)
        - Microsoft.AspNetCore.All
        - Microsoft.AspNetCore.App
        - Microsoft.WindowsDesktop.App
    - `-t <theme.json>` or `--theme <theme.json>`: Read a theme file for syntax highlighting. The [NO_COLOR](https://no-color.org/) standard is supported.
    - `-v` or `--version`: Show version number and exit.
    - `-h` or `--help`: Show this help and exit.
- `response-file.rsp`: A filepath of an .rsp file, containing any of the above command line options.
- `script-file.csx`: A filepath of a .csx file, containing lines of C# to evaluate before starting the REPL.

## Integrating with other software

### Windows Terminal

To add the C# REPL as a menu entry in Windows Terminal, add the following profile to Windows Terminal's `settings.json` configuration file (under the JSON property `profiles.list`):

```json
{
    "name": "C# REPL",
    "commandline": "csharprepl"
},
```

### Visual Studio Code

To use the C# REPL with Visual Studio Code, simply run the `csharprepl` command in the Visual Studio Code terminal. To send commands to the REPL, use the built-in `Terminal: Run Selected Text In Active Terminal` command from the Command Palette (`workbench.action.terminal.runSelectedText`).

<p align="center">
  <img src="https://raw.githubusercontent.com/waf/CSharpRepl/main/.github/readme_assets/vscode.png" alt="Visual Studio Code screenshot" style="max-width:90%;">
</p>