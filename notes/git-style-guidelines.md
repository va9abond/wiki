## TOC
- [Branch Working Guidelines](#Branch-Working-Guidelines)
- [Commit Message Guidelines](#Commit-Message-Guidelines)
- [Issues and Bugs](#Issues-and-Bugs)





## Branch Working Guidelines
### Naming

<div align="center">

    git branch reference/category/fair-decs

</div>



#### Categories

    * feat     - adding/removing/modifying a feature
    * fix      - path error/bug
    * test     - branch for experiments
    * refactor - refactor features, functions, classes, ...


Example:
- `3/fix/mst-memory-leak`
- `2/feat/recode-verts-as-pointers`
- `722/feat/add-billing-module`
- `421/wip/optimize-sorting-algo`
- `feat/add-combo-attack` (drop `no-ref`)



#### Principles

- Avoid long branch names
- Be consistent





## Commit Message Guidelines
### Principles

<div align="center">
    Commit should be an indivisible logical unit of code
</div>


#### Commit related changes
A commit should be a wrapper for related changes. For example,
fixing two different bugs should produce two separate commits.


#### Commit often
Committing often keeps your commits small that makes it easier for
other developers to understand the changes and roll them back
if something went wrong. With tools like the staging area and
the ability to stage only parts of a file, Git makes it easy
to create very granular commits.


#### Don't commit half-done work (no, i can cat:checkout)
You should only commit code when a logical component is
completed. Split a feature‘s implementation into logical
chunks that can be completed quickly so that you can commit
often. If you‘re tempted to commit just because you need a
clean working copy consider using Git‘s «Stash» feature instead.


#### Test code before commit
Your code is terrible and useless until you prove otherwise.


#### Write good commit messages

- The `description` should express the main meaning of your commit
  (<= 50 chars)
- Imperative style: "Fix bug" and not "Fixed bug" or "Fixing bug"
  (to follow auto generated messages from `git merge`/`git revert`)
- Do not capitalize the first letter and not dot(.) at the end
- The second line is always empty
- `Body` part should express detailed definition of your commit,
  plus your contacts to connect; wrap this section about 72 chars
- See `Format` section for more


#### Use branches
Branching a project is another way to logically separate it



### Format

    <type>[(<scope>)]: <desc>

    [<body>]

    [<footer>]

#### Type

    * chore      - change code style, add/clear comments/docs, formatting
    * feat       - adding/removing/modifying a feature (require #no)
    * fix        - patch error/bug
    * refactor   - recode function/class/script/...
    * wip        - work in progress: write function/class/script/test/docs/...
    * checkpoint - to save some changes (not enough for wip)
    * revert     - if the commit reverts a previous one
    * repo       - changes related just with repo and do not impact on code base explicitly
                  (eg moving/renaming folders/files, editing README.md)
    * readme     - update readme file

#### Scope
Example:

- `fix(llist-node): mark List_node._Pnext as pointer`
- `refactor!(iterator): change the return value of .parent()`

> **Note**
I use <u>\<type\>!</u> with **!** when changes may impact on
other code or if they are mean BREAKING CHANGES


#### Footer
The footer should contain any information about Breaking Changes
and is also the place to reference GitHub issues that this
commit closes.

Example:

    fix(graph-mst): patch leak in loop

    [...]

    Close #3





## Issues and Bugs
