# tiny-slsa-demos
minimum viable demos to explain SLSA v0.1 Levels 0 to 4


# Design Choices

## tiny-slsa-0

It was surprisingly non-trivial to make an app that had SLSA Level 0. To achieve this,

- I hosted code on replit.com -- which allows for anybody to anonymously edit the code at any time, with no version control or provenance
- I included manual build instructions

## tiny-slsa-1

Code is still hosted on replit.com! The differences from Level 0 are:

- I now include a Makefile which allows for a scripted build process
- Basic provenance documentation is generated (`provenance.txt`) when building the file, but this is a basic text file that is easily tampered and unauthenticated.

## tiny-slsa-2

It is surprisingly non-trivial to make a Level 2 app without letting it become Level 3. The differences from Level 1 are:

- Source code is now hosted on GitHub, which uses Git as version control for source code
- A hosted build service, GitHub Actions, is now responsible for creating and authenticating provenance every build. This is achieved by having provenance data built as part of the GitHub Actions workflow, which is then uploaded as a artifact alongside the built binary. That said, the provenance is still a basic text file.
- As part of the GitHub Action, a random file is created and then cached, so it is shared across different builds. This makes the build **not** isolated since builds are not independent of other build instances.

## tiny-slsa-3

The differences from Level 2 are:

- Commits are now signed, and version control history must be kept for at least 18 months
- The random cached file is no longer part of the GitHub Action, which means that the builds are isolated and in ephemeral environments (a fresh Ubuntu VM that is spun up)
- Build files are now treated "as code" -- for example, the go.sum checksum file is stored in version control so that build processes can be recreated if necessary
- Provenance is now generated using the in-toto attestation standard, and provenance is now therefore non-falsifiable and can be verified with slsa-verifier

## tiny-slsa-4

The differences from Level 3 are:
- Two-person review is required for any new commits
- Version control history is kept forever
- Builds are parameterless (this is actually already achieved before) -- basically the build script must be able to run without any inputs
- Build process is **hermetic** -- dependency references are **immutable** (eg. commit hash), dependency hashes are verified
- *Best attempt* at making build process **reproducible** -- re-running the build steps with identical input artifacts results in bit-for-bit identical output. The workflow is deterministic, and dependencies are vendored as much as possible. In this case, the Go package dependencies are vendored (and that part is reproducible), but Go itself and the Ubuntu VM are not.