# Security Policy

## Supported versions

Until the first tagged release, reports affecting the current default branch will be considered. After releases begin, the latest release receives priority; older releases may no longer receive security updates.

## Report a vulnerability

Do not publish vulnerability details in issues, pull requests, discussions, or public comments. If the repository offers GitHub's private **Report a vulnerability** form, use it. Otherwise, use the [Potoo contact page](https://diegopn.github.io/#contato) only to request a private reporting channel; do not include technical details or a proof of concept in that first message.

After arranging a private channel, include the affected version or commit, Rust version, operating system, reproduction steps, impact, and a minimal `VisuAlg` example. Remove credentials, personal information, and unrelated data.

## Security boundaries

Programs execute inside the host process and are not an operating-system sandbox. `RuntimeIo` delegates input, output, cancellation, waits, and `arquivo` data access to the host. Hosts implementing `arquivo` must confine paths and enforce access policy; VisuAlg random functions are not cryptographic. Apply process isolation when running untrusted source in a privileged or shared service. Resource limits reduce excessive execution but do not guarantee isolation or prevent every denial-of-service condition. Keep suitable limits enabled when accepting source from users.
