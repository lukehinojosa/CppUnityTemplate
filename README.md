# C++ / Unity Template

A blank C++ project and a blank Unity project in one repo, built by GitHub Actions on every push. The Unity project builds for the web with WebGPU and is deployed to GitHub Pages.

```
CMakeLists.txt  C++20 project (CMake)
src/            C++ sources
Unity/          Unity 6000.6.5f1 project (URP, WebGPU)
.github/        CI: builds both, deploys the Unity web build to Pages
```

## C++

Requires CMake 3.25+ and a C++20 compiler. Open the folder in CLion or Visual Studio, or:

```bash
cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
```

```bash
cmake --build build
```

Add new source files to `add_executable` in `CMakeLists.txt`.

## Unity

Open `Unity/` with Unity 6000.6.5f1 and the **Web** build support module. The web player uses the WebGPU graphics API (**Project Settings > Player > Web > Other Settings > Graphics APIs**), so it needs a browser with WebGPU enabled (current Chrome or Edge).

Brotli compression is on with **Decompression Fallback** enabled, because GitHub Pages cannot send the `Content-Encoding` header Unity's compressed files need.

## CI (GitHub Actions)

`.github/workflows/build.yml`:

| Job | What it does |
| --- | --- |
| `cpp` | Builds and runs the C++ project on Ubuntu, Windows and macOS, uploads the executables. |
| `webgl` | Builds the Unity web player with [GameCI](https://game.ci) and packages it for Pages. |
| `deploy` | On pushes to the default branch, publishes the web build to GitHub Pages. |

One-time setup on GitHub:

1. **Settings > Pages > Build and deployment > Source:** GitHub Actions.
2. **Settings > Secrets and variables > Actions**, add:
   - `UNITY_EMAIL` and `UNITY_PASSWORD`
   - `UNITY_LICENSE` (Personal: the contents of your `.ulf` file, see [GameCI activation](https://game.ci/docs/github/activation)) **or** `UNITY_SERIAL` (Plus/Pro)

Without a license secret the Unity jobs are skipped with a warning and the C++ job still runs.

GameCI picks the Unity version from `Unity/ProjectSettings/ProjectVersion.txt`. If it has no Docker image for that version yet, set `unityVersion:` on the build step to the closest one that exists.

## Making it yours

- Rename the project in `CMakeLists.txt` (`project(...)`; the executable takes the same name, so update the `Run` and upload steps in the workflow too).
- Change the Unity product name in **Project Settings > Player**.
