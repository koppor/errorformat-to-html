# Error Format as HTML

This tool renders errors in an `errors.txt` file following the [errorformat](https://vimdoc.sourceforge.net/htmldoc/quickfix.html#errorformat) as HTML.
It currently sticks to `%f:%l:%c:%e:%k: %t%n %m` as concrete format.

Example input:

```errorformat
texmf-dist/tex/fontinst/base/fontinst.sty:1324:40:1324:40: e209 invalid characters
```

It is currently bound to the output of [`explcheck`](https://github.com/Witiko/expltools/), which performs static analysis on expl3 programs.
In a GitHub action `explcheck` is called and the result rendered as HTML page.

## Development Hints

- Served files: <https://github.com/koppor/explcheck-issues/tree/gh-pages> - one commit - overwritten with latest state
- Rendered output: <https://koppor.github.io/explcheck-issues>

### Required Development Setup on Windows

- Enable symlinks with git (<https://stackoverflow.com/a/59761201/873282>):
  Press <kbd>Win</kbd> + <kbd>R</kbd>, type `gpedit.msc`, hit OK. Then navigate to Local Comp Policy > Computer Configuration > Windows Settings > Security Settings > Local Policies > User Rights Assignment > Create symbolic links

Install `errorformat`:

```bash
go install github.com/reviewdog/errorformat/cmd/errorformat@latest
```

### Serve HTML

Run simple HTTP server (useful when running a checkout of `gh-pages` branch):

```bash
npx http-server
```

This also works inside `pages/file`.

In case you don't see updates, enable "Disable Cache" in the tab "Network" in Firefox DevTools.

### Create `errors.txt` and `errors.json`

Local run:

```bash
docker run ghcr.io/witiko/expltools/explcheck -v "...":/workdir --porcelain --error-format='%f:%l:%c:%e:%k: %t%n %m' /workdir/expltools/explcheck/testfiles/e102.tex > errors.txt
errorformat -w jsonl "%f:%l:%c:%e:%k: %t%n %m" < errors.txt | jq -s > errors.json
```

On Windows cmd, one needs to enquote the `--error-format` with double quotes.

```cmd
docker run --rm -v "c:\\git-repositories\\koppor\\explcheck-issues:/workspace" ghcr.io/witiko/expltools/explcheck --porcelain --error-format="%f:%l:%c:%e:%k: %t%n %m" "/workspace/expltools/explcheck/testfiles/e102.lua"
```

### Scripts

#### `create-dirs-for-files.sh`

- Execute in root of repository.
- Ensure that `errors.json` exists here. Use `pages/main/errors.json`.
- Ensure that `errors.txt` exists here. Use any (e.g., <https://koppor.github.io/explcheck-issues/expltools/explcheck/testfiles/e102-01.tex/errors.txt>)

## Acknowledgements

This project makes use of [errorformat](https://github.com/reviewdog/errorformat) and [jq](https://jqlang.github.io/jq/).
