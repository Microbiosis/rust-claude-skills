# Rust Claude Code Skills

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.20502951.svg)](https://doi.org/10.5281/zenodo.20502951)

Claude Code skills for Rust language development, based on official Rust documentation.

## Skills Overview

| Skill | Source | Description |
|-------|--------|-------------|
| `rust-language` | rust-lang/book, rust-by-example | Core language reference |
| `rust-cli` | rust-cli/book | CLI tool development |
| `rust-embedded` | rust-embedded/book | Embedded development |
| `rust-advanced` | rust-lang/nomicon, reference | Advanced features (unsafe, FFI) |
| `rust-tools` | rust-lang/rust (rustdoc, rustc) | Toolchain usage |
| `rust-std` | doc.rust-lang.org/std | Standard library reference |
| `rust-edition` | rust-lang/edition-guide | Edition differences and migration |
| `rust-unstable` | rust-lang/rust/unstable-book | Unstable features (nightly) |
| `rust-exercises` | rust-lang/rustlings | Rust exercises and learning resources |
| `rust-reference` | rust-lang/reference | Language specification and details |
| `rust-macros` | rust-lang/rust (macros) | Macro system reference |

## Installation

### Option 1: Copy to Claude Skills Directory

```bash
# Copy skills to Claude's skills directory
cp -r rust-* ~/.claude/skills/
```

### Option 2: Symbolic Links

```bash
# Create symbolic links
ln -s /path/to/rust-claude-skills/rust-language ~/.claude/skills/rust-language
ln -s /path/to/rust-claude-skills/rust-cli ~/.claude/skills/rust-cli
# ... etc
```

## Usage

Once installed, the skills will be automatically triggered when you:

- Write or modify Rust code (`.rs` files)
- Ask about Rust syntax or features
- Write or run tests
- Work with unsafe code
- Use `cargo` commands

You can also explicitly invoke them:

```
/rust-language    # Language reference
/rust-cli         # CLI development
/rust-embedded    # Embedded development
/rust-advanced    # Advanced features
/rust-tools       # Toolchain usage
/rust-std         # Standard library
/rust-edition     # Edition guide
/rust-unstable    # Unstable features
/rust-exercises   # Learning exercises
/rust-reference   # Language reference
/rust-macros      # Macro system
```

## Source Repositories

| Repository | License |
|------------|---------|
| [rust-lang/book](https://github.com/rust-lang/book) | MIT/Apache-2.0 |
| [rust-lang/rust-by-example](https://github.com/rust-lang/rust-by-example) | Apache-2.0 |
| [rust-lang/rustlings](https://github.com/rust-lang/rustlings) | MIT |
| [rust-lang/edition-guide](https://github.com/rust-lang/edition-guide) | Apache-2.0 |
| [rust-lang/reference](https://github.com/rust-lang/reference) | Apache-2.0 |
| [rust-lang/nomicon](https://github.com/rust-lang/nomicon) | Apache-2.0 |
| [rust-embedded/book](https://github.com/rust-embedded/book) | Apache-2.0 |
| [rust-cli/book](https://github.com/rust-cli/book) | MIT |
| [rust-lang/rust](https://github.com/rust-lang/rust) | MIT/Apache-2.0 |
| [doc.rust-lang.org/std](https://doc.rust-lang.org/std) | MIT/Apache-2.0 |

## License

This project is based on official Rust documentation and is subject to their licensing terms:

- **Apache-2.0**: rust-by-example, edition-guide, reference, nomicon, rust-embedded/book
- **MIT**: rustlings, rust-cli/book
- **MIT/Apache-2.0**: book, rust (rustdoc, rustc, unstable-book), std

See [LICENSE](LICENSE) for details.

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## Acknowledgments

Thanks to the Rust team and community for creating excellent documentation that made these skills possible.
