## NixOS

- 我们需要`glfw3`和`glew`, 并且需要让其能够被`pkgconf`发现
  (普通情况下直接安装的库文件是与应用程序隔离的, 参见 [NixOS FAQ](https://nixos.wiki/wiki/FAQ#I_installed_a_library_but_my_compiler_is_not_finding_it._Why.3F)), 所以我们需要`nix-shell`

- 可以直接用`nix-shell -p pkgconf glfw-wayland glew-egl glm wayland libffi`来构建计算机图形学的bash环境
  进入nix-shell环境后, 假如我们的主程序文件名为`main.cpp`, 
  这时我们可以通过`g++ $(pkgconf --cflags glfw3 glew) $(pkgconf --libs glfw3 glew) -o main main.cpp`来进行编译

- 或者用`shell.nix`
  ```nix
    let 
      pkgs = import <nixpkgs> { };
    in
      pkgs.mkShell {
        name = "cg-dev-env";
        nativeBuildInputs = with pkgs; [
          pkgconf
          cmake
        ];
        buildInputs = with pkgs; [
          # for debug
          gdb
          glfw-wayland
          # NOTE: package glew will cause error(?)
          glew-egl
          glm
          # required by glfw-wayland
          wayland
          # required by wayland-client
          libffi
        ];

        shellHook = ''
          exec zsh
        '';
      }
  ```
  将上述文件保存为`shell.nix`后运行`nix-shell`可进入含有所需工具的zsh环境
