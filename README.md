# Nix Flake for Ollama [![built with nix](https://builtwithnix.org/badge.svg)](https://builtwithnix.org)

## For NixOs users or if you have nix package manager installed on system:
No need to bloat your system with ollama and models when developing. Copy below flake and place in desired directory
First time setting up enviornment if cuda enabled will take a some time to compile from source 
```bash
nix develop
```

```bash
ollama serve
```

```bash
ollama run <model>
```

```nix
{
  description = "Development environment for Ollama";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-unstable";
    flake-utils.url = "github:numtide/flake-utils";
  };

  outputs = { self, nixpkgs, flake-utils, ... }:
    flake-utils.lib.eachDefaultSystem (system:
      let
        pkgs = import nixpkgs {
          system = system;
          #config.allowUnfree = true; #uncomment if using cuda/gpu acceleration
          #config.cudaSupport = true; #uncomment is using cuda/gpu acceleration
          
        };
      in {
        devShell = pkgs.mkShell {
          buildInputs = [
            pkgs.ollama
          ];

          # Customized shell
          shellHook = ''
            export PS1="\[\033[1;34m\][nix-dev:\w]$ \[\033[0m\]"
            echo -e "\033[1;32m==> Entering Ollama Development env on ''${system}\033[0m"
            echo -e "\033[1;34mPackages available:\033[0m"
            echo ''${buildInputs} | tr ' ' '\n' | sed 's/^/  - /'
          '';
        };
      }
    );
}
```
