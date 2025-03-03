UV is a a python package and project manager, written in rust

See [UV documentation](https://docs.astral.sh/uv/)

```bash
pip install uv
uv python install
```

The `uv python install` installs the latest version of python

Start a package-managed project `uv init <project_name>`

```bash
uv init my-app
cd my-app
code .
```

```bash
uv venv --python 3.12
.venv\Scripts\activate
```

## How to use uv with jupyter on vscode

```bash
uv add --dev ipykernel
```

## How to add a local package

```bash
uv pip install "muspan @ ./package-muspan"
```

