# Contributing to Portugol VisuAlg

Contributions to the Portugol VisuAlg interpreter, its tests, and its documentation are welcome.

## Report a bug

Open an issue in the repository hosting this crate. Include the crate version or commit, Rust version, operating system, a small `.alg` example, expected and actual behavior, and steps to reproduce the issue. Remove credentials, personal information, and unrelated project data.

## Development setup

Install Rust 1.93 or later. From the repository root, run:

```sh
cargo fmt --all -- --check
cargo check
cargo test
```

Before proposing a release, also check that the crate packages:

```sh
cargo package
```

## Changes to language behavior

- Add tests for new syntax, runtime behavior, diagnostics, and resource limits where applicable.
- Keep the classic VisuAlg 3.0.5.2 target explicit; do not claim compatibility with unverified versions or forks.
- Keep public documentation in English and update it when the API or supported behavior changes.
- Keep changes focused and list the verification commands you ran.
- Preserve existing copyright and license notices. Check the license of any copied third-party material and disclose added dependencies with their purpose and license.

## License for contributions

Contributions are distributed under the GNU General Public License version 3 or, at your option, any later version. See [LICENSE](LICENSE).
