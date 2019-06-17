# Nix setup for Haskell

Please refer to [haskell-nix](https://github.com/Gabriel439/haskell-nix) for the complete guide, this is a stripped down version.

-----------------------------------------

Globally install cabal:
```
nix-env --install cabal-install
nix-env --install cabal2nix
```

Init cabal project without install ghc globally, cabal will pull in ghc afterwards:
```
nix-shell --packages ghc --run 'cabal init'
```

Add `default.nix`, note we have added ghcid:
```nix
{ pkgs ? import <nixpkgs> { } }:
pkgs.mkShell {
  buildInputs = [ pkgs.haskellPackages.ghcid ];
}
```

Now run
```
nix-shell
```

we are now inside nix-shell, create project.nix:
```
cabal2nix . > project.nix
```

change `default.nix` to
```nix
{ pkgs ? import <nixpkgs> { } }:
let
  project = pkgs.haskellPackages.callPackage ./project.nix { };
in pkgs.mkShell {
  buildInputs = [ pkgs.haskellPackages.ghcid ];
  inputsFrom = [ project.env ];
}
```

exit nix-shell (ctrl-D) and `nix-shell` enter again, run
```
ghcid "--command=ghci Main.hs" --test=":main"
```
and we are good to go, modifying Main.hs will rerun it.
