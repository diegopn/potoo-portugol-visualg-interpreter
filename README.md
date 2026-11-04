# Portugol VisuAlg Interpreter

A reusable Rust interpreter library for the classic Portugol VisuAlg 3.0.5.2 pseudolanguage. It has no third-party dependencies and can be embedded in Rust applications; it includes a standalone terminal command-line runner.

The compatibility target is classic VisuAlg 3.0.5.2. The linked online archive labels the manual pages 3.0.5.2, while the built-in functions page identifies its catalog as VisuAlg 2.0, so the source version is not fully consistent. Compatibility testing is ongoing; the crate does not invoke or bundle the original VisuAlg application.

## Source example

```alg
algoritmo "exemplo"
inicio
  escreval("Olá, VisuAlg!")
fimalgoritmo
```

## Use

Add the crate as a dependency:

~~~toml
[dependencies]
potoo-visualg-interpreter = { git = "https://github.com/diegopn/potoo-portugol-visualg-interpreter.git", rev = "515d415298a4ce336338584177b7905d263dfe76" }
~~~

Implement RuntimeIo to provide input and output, then call execute:

~~~rust
use potoo_visualg_interpreter::{InputPrompt, RuntimeIo, execute};

struct Console;

impl RuntimeIo for Console {
    fn write(&mut self, text: &str) {
        print!("{text}");
    }

    fn read_line(&mut self, prompt: &InputPrompt) -> Result<String, String> {
        eprint!("{} ({:?}): ", prompt.variable_name, prompt.data_type);
        let mut input = String::new();
        std::io::stdin()
            .read_line(&mut input)
            .map_err(|error| error.to_string())?;
        Ok(input.trim_end_matches(['\r', '\n']).to_owned())
    }
}

fn main() -> Result<(), Vec<potoo_visualg_interpreter::Diagnostic>> {
    let source = r#"algoritmo "exemplo"
inicio
  escreval("Olá, VisuAlg!")
fimalgoritmo
"#;
    execute(source, &mut Console)
}
~~~

Call `parse` instead of `execute` when a host needs to inspect the public syntax tree without running the program.

RuntimeIo::is_debugging enables statement events. RuntimeIo::on_execution_event receives the current source position, visible variable values, and pause reason; return Continue, Step, or Stop to control execution. Hosts can also implement should_stop to interrupt CPU-bound loops, wait for timer, and the data-file callbacks for arquivo. The default wait implementation sleeps for the requested duration; the default data-file callbacks return an error so a host without file support cannot silently ignore the directive.

## Supported language features

- algoritmo, declaration sections, procedures, functions, typed parameters passed by value or reference, recursion, and retorne
- inteiro, real, caractere, and logico, plus vectors and matrices with explicit lower and upper bounds
- Assignments, arithmetic and logical expressions, comparisons, text operations, and formatted escreva / escreval
- Typed leia, se / senao, escolha / caso / outrocaso, enquanto, repita / ate, para, and interrompa
- Numeric and text functions: Abs, ArcCos, ArcSen, ArcTan, Asc, Carac, Caracpnum, Compr, Copia, Cos, CoTan, Exp, GrauPrad, Int, Log, LogN, Maiusc, Minusc, Numpcarac, Pi, Pos, Quad, RaizQ, Rand, RandI, RadpGrau, Sen, and Tan
- Runtime commands aleatorio, arquivo, timer, pausa, debug, eco, cronometro, and limpatela
- Source-located lexical, syntax, and runtime diagnostics, and debugger snapshots that include array elements

The arquivo command delegates storage to RuntimeIo; the interpreter itself does not access the filesystem. The IDE host confines data-file paths to the open project. Numeric random ranges, character generation, timers, and debugger pauses follow the classic manual. The IDE host has no separate simulated DOS screen, so `limpatela` has no visible effect there; other hosts can implement `RuntimeIo::clear_screen` when they provide that screen.

## Runtime limits

execute_with_options accepts explicit limits. Defaults for one run are 1,000,000 statement and loop steps, 1 MiB of output, 1 MiB per string, 500 variable and array-element slots, 1 MiB of data-file contents, 30 seconds of total timer wait, 32 nested routine calls, and 128 levels of parser nesting.

The parser reports the first lexical or syntax error. Runtime diagnostics include a one-based line and column. Matching the original application's behavior across the entire language remains an ongoing compatibility task.

Nested procedures and functions are not supported. The compatibility target is the classic 3.0.5.2 language described by the linked manual; other VisuAlg forks and versions have not been validated.

## Development

Run these commands from the repository root:

~~~sh
cargo fmt --all -- --check
cargo check
cargo test
~~~

## Project policies

- [Contributing](CONTRIBUTING.md)
- [Security policy](SECURITY.md)
- [GPL-3.0-or-later license](LICENSE)

## Standalone command-line runner

The `potoo-visualg` command runs one source file without Potoo IDE. For example:

```sh
potoo-visualg example.alg
```

The terminal runner provides standard input/output. `arquivo` data-file commands are unavailable and produce an unsupported-capability diagnostic. The runner does not provide an interactive debugger.

## Install a prebuilt runner

Download the archive for your system and `SHA256SUMS` from the [latest GitHub Release](https://github.com/diegopn/potoo-portugol-visualg-interpreter/releases/latest). Binary releases are not published yet. The planned first release will be tagged `v0.1.0`; its release workflow is configured to produce these asset names:

| System | Archive |
| --- | --- |
| Linux x86_64 | `potoo-portugol-visualg-interpreter-v0.1.0-x86_64-unknown-linux-gnu.tar.gz` |
| Windows x86_64 | `potoo-portugol-visualg-interpreter-v0.1.0-x86_64-pc-windows-msvc.zip` |
| macOS Intel | `potoo-portugol-visualg-interpreter-v0.1.0-x86_64-apple-darwin.tar.gz` |
| macOS Apple silicon | `potoo-portugol-visualg-interpreter-v0.1.0-aarch64-apple-darwin.tar.gz` |

CI passes on Linux x86_64, Windows x86_64, macOS Intel, and macOS Apple silicon. The release workflow is configured to build archives for these four targets; binary assets have not been published yet.

### Linux x86_64

```sh
asset=potoo-portugol-visualg-interpreter-v0.1.0-x86_64-unknown-linux-gnu.tar.gz
release=https://github.com/diegopn/potoo-portugol-visualg-interpreter/releases/download/v0.1.0
curl -fLO "$release/$asset"
curl -fLO "$release/SHA256SUMS"
sha256sum "$asset" # compare with its line in SHA256SUMS
tar -xzf "$asset"
mkdir -p "$HOME/.local/bin"
install -m 755 "potoo-portugol-visualg-interpreter-v0.1.0-x86_64-unknown-linux-gnu/potoo-visualg" "$HOME/.local/bin/potoo-visualg"
potoo-visualg example.alg
```

Add it for the current shell with `export PATH="$HOME/.local/bin:$PATH"`; place that line in your shell profile to keep it for future sessions.

### Windows x86_64 (PowerShell)

```powershell
$asset = "potoo-portugol-visualg-interpreter-v0.1.0-x86_64-pc-windows-msvc.zip"
$release = "https://github.com/diegopn/potoo-portugol-visualg-interpreter/releases/download/v0.1.0"
Invoke-WebRequest "$release/$asset" -OutFile $asset
Invoke-WebRequest "$release/SHA256SUMS" -OutFile SHA256SUMS
Get-FileHash ".\$asset" -Algorithm SHA256 # compare with the matching SHA256SUMS entry
Expand-Archive ".\$asset" -DestinationPath .\potoo-portugol-visualg-interpreter
.\potoo-visualg-interpreter\potoo-portugol-visualg-interpreter-v0.1.0-x86_64-pc-windows-msvc\potoo-visualg.exe .\example.alg
```

To run `potoo-visualg.exe` from any folder, add its extracted directory to your user `PATH` in Windows Environment Variables.

### macOS Intel and Apple silicon

Use the `x86_64-apple-darwin` archive on Intel Macs and `aarch64-apple-darwin` on Apple silicon.

```sh
asset=potoo-portugol-visualg-interpreter-v0.1.0-<target>.tar.gz
release=https://github.com/diegopn/potoo-portugol-visualg-interpreter/releases/download/v0.1.0
curl -fLO "$release/$asset"
curl -fLO "$release/SHA256SUMS"
shasum -a 256 "$asset" # compare with its line in SHA256SUMS
tar -xzf "$asset"
mkdir -p "$HOME/.local/bin"
install -m 755 "${asset%.tar.gz}/potoo-visualg" "$HOME/.local/bin/potoo-visualg"
potoo-visualg example.alg
```

Replace `<target>` with `x86_64-apple-darwin` or `aarch64-apple-darwin`. Add it for the current shell with `export PATH="$HOME/.local/bin:$PATH"`; place that line in `~/.zprofile` to keep it for future macOS sessions.

## Install from source

With Rust 1.93 or later and network access, Cargo can install the command directly from its tagged source:

```sh
cargo install --git https://github.com/diegopn/potoo-portugol-visualg-interpreter.git --rev 515d415298a4ce336338584177b7905d263dfe76 --bin potoo-visualg
```

## Use as a Rust library

```toml
[dependencies]
potoo-visualg-interpreter = { git = "https://github.com/diegopn/potoo-portugol-visualg-interpreter.git", rev = "515d415298a4ce336338584177b7905d263dfe76" }
```

The library API lets Rust applications provide their own `RuntimeIo` implementation.

## DOI

A stable Figshare DOI will be added when the first public release is published.
