<p align="center">
  <img src="https://diegopn.github.io/potoo-site/src/assets/images/language-interpreter-icon.svg?v=1c651ca7" alt="Potoo IDE language interpreter icon" width="120" />
</p>

<h1 align="center">Portugol VisuAlg Interpreter</h1>

<p align="center">A Rust interpreter library and CLI for the classic VisuAlg 3.0.5.2 pseudolanguage.</p>

<p align="center">
  <a href="https://github.com/diegopn/potoo-portugol-visualg-interpreter/releases/latest">Download</a> ·
  <a href="https://diegopn.github.io/potoo-site/">Website</a> ·
  <a href="https://diegopn.github.io/potoo-site/manual/visualg/">User manual</a>
</p>

<p align="center">
  <a href="https://www.rust-lang.org/"><img alt="Rust" src="https://img.shields.io/badge/Rust-000000?style=for-the-badge&logo=rust&logoColor=white"></a>
  <a href="https://doc.rust-lang.org/cargo/"><img alt="Cargo" src="https://img.shields.io/badge/Cargo-DEA584?style=for-the-badge&logo=rust&logoColor=white"></a>
  <img alt="Rust library" src="https://img.shields.io/badge/Rust_library-4A86CF?style=for-the-badge">
  <img alt="CLI" src="https://img.shields.io/badge/CLI-6A4C93?style=for-the-badge">
</p>

Originally developed as part of the Potoo IDE project, this interpreter is an independent Rust software package intended to be distributed separately from the IDE. It can be embedded as a library in other Rust projects or run as a standalone command-line tool on Linux, macOS, and Windows.

The crate has no third-party dependencies and can be embedded in Rust applications. The compatibility target is classic VisuAlg 3.0.5.2. The linked online archive labels its manual pages 3.0.5.2, while the built-in functions page identifies its catalog as VisuAlg 2.0, so the source version is not fully consistent. Compatibility testing is ongoing; the crate does not invoke or bundle the original VisuAlg application.

## Original language references

- [VisuAlg 3.0 project archive and documentation files](https://sourceforge.net/projects/visualg30/files/)

The archive contains the original VisuAlg software and reference files. This repository provides an independent Rust implementation.

## Original language references

- [VisuAlg 3.0 project archive and documentation files](https://sourceforge.net/projects/visualg30/files/)

The archive contains the original VisuAlg software and reference files. This repository provides an independent Rust implementation.

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

## Support

If this interpreter is useful in your work, you can support its continued development:

<p align="left">
  <a href="https://github.com/sponsors/diegopn"><img alt="Become a sponsor" src="https://img.shields.io/badge/Become_a_sponsor-EA4AAA?style=for-the-badge&logo=githubsponsors&logoColor=white"></a>
</p>

## Project policies

- [Code of conduct](CODE_OF_CONDUCT.md)
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

Prebuilt binaries are published on the [GitHub Releases page](https://github.com/diegopn/potoo-portugol-visualg-interpreter/releases). Choose a version and the package for your system there. Packages are currently available for Linux x86_64, Windows x86_64 (MSVC), and Intel macOS (x86_64). The current release does not include an Apple silicon (arm64) package.

Download only the package for your system and the `SHA256SUMS` file, and save both in the same folder. Before extracting or running the executable, compare the package's SHA-256 hash with its corresponding entry in `SHA256SUMS`. Run the commands below from that folder. Replace `example.alg` with the path to a source file you want to run; the release package does not include a sample file.

### Linux x86_64

```sh
asset=$(ls -1 potoo-portugol-visualg-interpreter-v*-x86_64-unknown-linux-gnu.tar.gz | head -n 1)
sha256sum "$asset" # compare with its line in SHA256SUMS
tar -xzf "$asset"
mkdir -p "$HOME/.local/bin"
install -m 755 "${asset%.tar.gz}/potoo-visualg" "$HOME/.local/bin/potoo-visualg"
export PATH="$HOME/.local/bin:$PATH"
potoo-visualg example.alg
```

To keep the command available in new Linux sessions, add the `export PATH="$HOME/.local/bin:$PATH"` line to your shell profile.

### Windows x86_64 (PowerShell)

```powershell
$archive = Get-ChildItem -Filter "potoo-portugol-visualg-interpreter-v*-x86_64-pc-windows-msvc.zip" | Select-Object -First 1
Get-FileHash $archive.FullName -Algorithm SHA256 # compare with the matching SHA256SUMS entry
Expand-Archive $archive.FullName -DestinationPath .\potoo-visualg
$exe = Get-ChildItem .\potoo-visualg -Filter "potoo-visualg.exe" -Recurse | Select-Object -First 1
& $exe.FullName .\example.alg
```

To run `potoo-visualg.exe` from another folder, add the directory containing the executable to your user `PATH`.

### Intel macOS

The published macOS package targets Intel Macs (x86_64). There is no prebuilt Apple silicon package in the current release.

```sh
asset=$(ls -1 potoo-portugol-visualg-interpreter-v*-x86_64-apple-darwin.tar.gz | head -n 1)
shasum -a 256 "$asset" # compare with its line in SHA256SUMS
tar -xzf "$asset"
mkdir -p "$HOME/.local/bin"
install -m 755 "${asset%.tar.gz}/potoo-visualg" "$HOME/.local/bin/potoo-visualg"
export PATH="$HOME/.local/bin:$PATH"
potoo-visualg example.alg
```

Add the `export PATH="$HOME/.local/bin:$PATH"` line to `~/.zprofile` to keep the command available in future macOS sessions.


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

## DOI and citation

DOI: [10.6084/m9.figshare.34915245](https://doi.org/10.6084/m9.figshare.34915245)

Suggested reference (ABNT NBR 6023:2018):

> NASCIMENTO, Diego Pereira do. *Portugol VisuAlg interpreter v0.1.0*. 2026. Programa de computador. Disponível em: https://doi.org/10.6084/m9.figshare.34915245. Acesso em: 4 nov. 2026.
