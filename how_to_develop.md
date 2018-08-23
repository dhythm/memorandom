## In the beginning

## Development flow
### Create/Fetch Repository
```
$ git clone <remote_repository>
```
### Create workspace branch
```
$ git branch
$ git checkout -b <new_branch>
```
### Write code
I recommend to use some powerful development tools for preventing human-mistakes.
For example, intellisense(snippet), tslint(eslint) and anything else.
Those tool can help you to prevent to make mistakes and enhance maintainability.

vscode can search code by `cmd + T`, `cmd + F`, `Shift + cmd + F`.
vscode can go to definition by `cmd + click` or `cmd + opt + click`.
vscode can find all references by `Shift + F12`.

### Run(Test)

[Jest](https://jestjs.io/en/) is one of the useful tool for development.
If you want to develop by TDD, it can support you strongly.
```
$ jest spec --watch #or
$ jest <filename>
```

### Send Pull Request
```
$ git status
$ git add -A
$ git commit -m "write comment here."
$ git branch
$ git push origin <new_branch>
```
### Review
### Merge to master branch
### Check by Product Team
### Deploy to Production

### The others(Appendix)
You want to cancel some `add` command, the below command will help you.
This command removes files from the index.
If you don't attach the option of `--cached`, the files are deleted from workspace.
```
$ git rm --cached <filename>
```

`git rebase` is very powerful (and dangerous) command, so I strongly recommend to read [document](https://git-scm.com/docs/).

```
$ git rebase --onto <newbase> <upstream> <branch>
```

## Tools
### Visual Studio Code
#### Extension
For now, I describe the plugins which is installed in my vscode installs.
I'll update this like adding the others and removing unused plugins near the future.
- Easy Sass
- GraphQL
- GraphQL for VSCode
- GraphQL Language Server Client
- Japanese Language Pack for Visual Studio Code
- JSON to TS
- Live Server
- Python
- Regex Previewer
- SQLite
- Vim
- Code Spell Checker

#### Configuration of Project
- .vscode
- .editorconfig
- .npmrc
- .prettierrc
- tsconfig.json
- tslint.json

#### Package.json (devDependencies)
- @types/jest
- jest
- jest-cli
- nodemon
- pre-commit
- prettier
- ts-jest
- ts-loader
- ts-node
- tsconfig-paths
- tslint
- tslint-config-prettier
- tslint-loader
- typescript
- typescript-snapshots-plugin


## Conclusion

