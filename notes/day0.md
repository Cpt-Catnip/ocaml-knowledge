# First pass at OCAML

* Going through the [Getting Started](https://ocaml.org/docs/up-and-running) tutorial on the OCAML site

Right off the bat, it seems like it's worth modifying `.zshrc` by adding 

```
[[ ! -r /Users/mkoshako/.opam/opam-init/init.zsh ]] || source /Users/mkoshako/.opam/opam-init/init.zsh  > /dev/null 2> /dev/null
```

otherwise...

> every time you want to access your opam installation, you will need to run:
> `eval $(opam env)`

I'm going to see if it's possible to run a command to add this line since it seems like I missed my chance to do it during `opam init`.

I tried manually adding that line to my `rc` and it's setting an older version of OCaml...

## `opam init`

Initializes an opam state (idk what that is) in `~/.opam` and the first switch. A switch in opam world is an independant OCaml environment with its own compiler, libraries, and binaries. Sounds similar to how python does it.

## `opam switch`

```bash
opam switch create <version_here>
```
Creates a new switch with the specified version of OCaml. I don't know if `switch` is for using different versions of ocaml or for different projects. Maybe both?

Getting new versions of ocaml seems to take a while...

## Base Tools

* OCaml comes with a base REPL command (similar to `node`) calles `ocaml`.
* a compiler to native code! Similar to `go build` I think. It's called `ocamlopt`. This creates an executable that can be executed by our system.
* A compiler to __bytecode__ calles `ocamlc`.

## Dev Tools!

```shell
opam install dune merlin ocaml-lsp-server odoc ocamlformat utop dune-release
```
This just installed
* Dune, a fast and full-featured build system for OCaml
* Merlin and ocaml-lsp-server (OCaml's Language Server Protocol), which together enhance editors (like Visual Studio Code, Vim, or Emacs) by providing many useful features such as "jump to definition"
* `odoc` to generate documentation from OCaml code
* OCamlFormat to automatically format OCaml code
* UTop, an improved REPL
* `dune-release` to release code to `opam-repository`, the central package directory of opam.

This is taking forever! Done!

## `utop`
* enhances ocaml toplevel repl
  * hashtag jargon

To tell ocaml that an expression ends, you need to end a line with `;;`. For example, in utop you would type

```ocaml
1 + 2 * 3;;
```
## Editor Config

The three most supported editors for ocaml are VSCode, Vim, and Emacs. Guess this is another reason to start learning vim :shrug:. I'll start off just using VSCode.

## Starting a new project

We do this all using `dune` I guess. So dune is for project management? Gonna make a project called `code/learning/hellocaml` lol.

`dune exec` to build and `dune run ./bin/main.exe` to run. There has to be an easier way to run built projects than that... Oh it's `dune exec hellocaml`. Nice.

Source code for the project is found in `./bin/main.ml` and any supporting library code should go in `lib`. We love to see convention.

## Autoformatting with OCamlFormat

It's good to enforce the version of the formatter in a config file. You can do this by running

```bash
echo "version = `ocamlformat --version`" > .ocamlformat
```

of by doing something similar manually. You can also run `dune fmt` to format your code. I love shit like that.

## document generation using `odoc`

Cool! Not meant to be used by hand. Dune can drive `odoc` because tooling in ocaml land rocks I guess.

Run `dune build @doc` to build the docs and `open _build/default/_doc/_html/index.html` to open them. Honstly awesome.