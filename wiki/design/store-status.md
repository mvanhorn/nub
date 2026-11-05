# Store status

`nub store status` verifies cached content hashes and required dependency directories in the configured global virtual store. Each published graph variant is checked independently under the store's exclusive maintenance lease.

The content pass verifies files named by cached CAS indices with BLAKE3. The dependency pass uses the resolved virtual-store path even when no CAS indices exist; an absent or empty virtual store is healthy.

Only physical installed packages under an entry's `node_modules` are inspected, including scoped and nested bundled packages. Dependency symlinks are checked as targets but never recursively traversed. Registry and staging bookkeeping are skipped.

For each manifest, required `dependencies` resolve by installed name through the package's own and ancestor `node_modules` directories, stopping at the graph entry boundary. Alias keys are installed names. Real bundled directories and links to directories both satisfy a dependency.

Names also declared in `optionalDependencies` are optional. Peer and development dependencies are excluded. Verification checks directory availability, not package entry points or semver compatibility, and never executes package code.

Missing or dangling required targets report the graph entry, manifest, dependency name and lookup path. Content corruption and missing dependencies have separate counts. Manifest parse errors and traversal failures return errors instead of reporting consistency.

Status does not repair links, diagnose how they disappeared, or inspect project-local installations. Its graph boundary follows the package isolation described in [[architecture]].
