# nix-systems-default

Flake with my default systems list for use with
[flake-utils](https://github.com/numtide/flake-utils).

## Usage

Example of a simple flake that uses `nix-systems-default` to define the systems
supported.

```nix
{
  inputs = {
    nixpkgs.url = "github:nixos/nixpkgs?ref=nixos-unstable";

    systems.url = "github:vpayno/nix-systems-default";

    flake-utils = {
      url = "github:numtide/flake-utils";
      inputs.systems.follows = "systems";
    };
  };

  outputs =
    {
      nixpkgs,
      flake-utils,
      ...
    }:
    flake-utils.lib.eachDefaultSystem (
      system:
      let
        pkgs = nixpkgs.legacyPackages.${system};
      in
      {
        packages = rec {
          hello = pkgs.hello;

          default = hello;
        };
      }
    );
}
```

Example `nix` command output:

- `nix flake show`

```text
$ nix flake show
git+file:///home/vpayno/git_vpayno/nix-systems-default
└───packages
    ├───aarch64-darwin
    │   ├───default omitted (use '--all-systems' to show)
    │   └───hello omitted (use '--all-systems' to show)
    ├───aarch64-linux
    │   ├───default omitted (use '--all-systems' to show)
    │   └───hello omitted (use '--all-systems' to show)
    ├───riscv64-linux
    │   ├───default omitted (use '--all-systems' to show)
    │   └───hello omitted (use '--all-systems' to show)
    ├───x86_64-darwin
    │   ├───default omitted (use '--all-systems' to show)
    │   └───hello omitted (use '--all-systems' to show)
    └───x86_64-linux
        ├───default: package 'hello-2.12.1'
        └───hello: package 'hello-2.12.1'
```

- `nix run`

```text
$ nix run
Hello, world!
```

- `nix run .#default`

```text
$ nix run .#default
Hello, world!
```

- `nix run .#hello`

```text
$ nix run .#hello
Hello, world!
```

- `nix build .#hello`

```text
$ nix build .#hello

$ ls -lh ./result
lrwxrwxrwx 1 vpayno vpayno 56 Mar 21 22:08 ./result -> /nix/store/wvpzaycmvs39h5bcsfrxkjsg48mj4h73-hello-2.12.1

$ ./result/
bin/   share/

$ ./result/bin/hello
Hello, world!
```

## Formatting

Looking to keep `flake.nix` simple, so not defining the `formatter` output, or
an `fmt` `apps` output. Instead just using the `fmt` app from another flake.

```text
$ nix run  github:vpayno/nix-treefmt-conf#fmt
2025/03/23 08:28:52 INFO using config file: /nix/store/d399ya7sivz9pg488zy0p6l8x7q83bnp-treefmt.toml
traversed 7 files
emitted 5 files for processing
formatted 1 files (0 changed) in 39ms
```
