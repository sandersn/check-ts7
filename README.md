# @sandersn/check-ts7

Lists what still blocks a TypeScript 7 upgrade in a repository:

- every `typescript` declaration that does not allow 7.x, in the root package, workspace packages (npm, yarn, pnpm, lerna, rush and bolt workspaces) and top-level packages that have their own lockfile, including `peerDependencies`, `overrides`, `resolutions` and `pnpm.overrides`;
- `typescript` declarations that were deleted instead of upgraded, compared with a git baseline;
- tsconfig options that TypeScript 7 removed or rejects (`baseUrl`, `moduleResolution: "node10"`, `target: "es5"`, `importsNotUsedAsValues`, `outFile`, `esModuleInterop: false`, `outDir` without `rootDir`, mismatched `module`/`moduleResolution`, …), checked on each config's effective options after `extends`.

It only reads files. It checks declarations and configuration, not code, so a clean report does not replace building and testing with TypeScript 7.

## Usage

Run it from anywhere inside the project's git repository:

```sh
npx -y @sandersn/check-ts7
```

To also find declarations that were deleted, pass the commit from before the upgrade:

```sh
npx -y @sandersn/check-ts7 --baseline <git-ref>
```

`--baseline` defaults to `HEAD`.

Exit codes: `0` when nothing is reported, `1` when problems remain, `2` for usage errors.

## Rules

- An installed declaration (`dependencies`, `devDependencies`, `optionalDependencies`, overrides) passes when its lowest allowed version is 7.x.
- A `peerDependencies` range passes when it accepts some 7.x version.
- The side-by-side layout for tools that still need the TypeScript API is accepted: `"typescript": "npm:@typescript/typescript6@^6"` passes when the same package or the workspace root declares `"@typescript/native": "npm:typescript@^7"`.
- `workspace:`, `catalog:`, `file:`, `link:` and git specifiers are skipped.
- A tsconfig problem shared by many configs is reported once, at the config that sets the option, with the configs that inherit it.
- A config that `extends` a package which is not installed is reported as unreadable; install dependencies and rerun.

## Development

```sh
npm install
npm test
```

## License

MIT
