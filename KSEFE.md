# Kiota for the Ksefe SDK

This fork builds the Kiota that generates the [Ksefe SDK](https://github.com/ksefe/sdk):
an upstream release plus fixes that upstream has not shipped yet. The SDK downloads
its binaries from this fork's releases and pins them by SHA-256. It is not a
general-purpose distribution of Kiota.

## Branches and tags

- `main` mirrors `microsoft/kiota` and is not changed here.
- `ksefe/v<version>` is the upstream tag `v<version>` with the patches below on top.
- `v<version>-ksefe.<n>` tags a build of that branch. Pushing such a tag runs
  `.github/workflows/ksefe-release.yml`, which tests the builder, publishes
  `<rid>.zip` for linux-x64, linux-arm64, win-x64, osx-x64 and osx-arm64 in the
  upstream layout, and `SHA256SUMS`. The binary reports `<version>-ksefe.<n>`.

Only the release workflow runs here. The upstream workflows trigger on a push to
`main`, a pull request or a schedule; nothing pushes `main` in this fork, so
GitHub never registers them. Do not press GitHub's "Sync fork" on `main`: that
push registers and runs all of them. Fetch upstream locally instead, as below.

## Patches

| Patch | Languages | Upstream |
| --- | --- | --- |
| A property typed as a discriminated `oneOf` of objects deserializes through the union's factory, not as its first member | TypeScript | [microsoft/kiota#6615](https://github.com/microsoft/kiota/issues/6615), PR [#8498](https://github.com/microsoft/kiota/pull/8498) |
| A union wrapper's factory matches every discriminator value mapped to a member, not only the first | C#, Python, PHP | not filed yet |

Each patch carries its tests.

## Update to a new upstream release

```sh
git fetch upstream --tags
git switch -c ksefe/v<new> v<new>
git cherry-pick v<old>..ksefe/v<old>   # drop the commits upstream has merged
git push origin ksefe/v<new>
git tag v<new>-ksefe.1 && git push origin v<new>-ksefe.1
```

Upstream keeps `CHANGELOG.md` entries in its own pull requests; on a conflict
there, keep the release's file.

## Retire the fork

When an upstream release carries every patch, point the SDK's
`scripts/install-kiota.mjs` back at `microsoft/kiota` and stop tagging here.
