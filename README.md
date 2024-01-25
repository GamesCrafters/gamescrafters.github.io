# [GamesCrafters Website](https://gamescrafters.github.io/)

This is a website containing information about the GamesCrafters computational game theory research group at UC Berkeley.

## Development

To contribute, you will need to install the [Go compiler toolchain](https://go.dev/doc/install) and [Hugo](https://gohugo.io/installation/), a static site generator. After that, make sure to clone the repository with submodules:

```zsh
git clone --recurse-submodules <SSH/HTTPS URL> <TARGET DIRECTORY>
```

Once you have the repository, you can do the following to start a local web server that supports hot reloading:

```zsh
cd <TARGET DIRECTORY>
hugo server
```

When you add a commit to the `main` branch, your changes are automatically deployed to GitHub Pages through a workflow.
