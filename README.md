# auto

## Gobot

This workflow is designed to automate the process of building and deploying godot projects.

## Features

- Caches godot and templates, so only downloads once.
- Parses build settings from `export_presets.cfg` to generates a custom strategy matrix to run all builds concurrently.
  - Do not need to set file names or paths just have the platform present in the file.
- Deletes old repository GitHub Pages deployments.
- Deploys web artifact to the repository GitHub Pages environment.

> [!WARNING]
> Old repository GitHub Pages deployments will be deleted.

## Workflow Overview

### Inputs

- `godot-version`: The version of Godot to be used in the build. This is required. Default: '4.3'
- `delete-old-pages`: Delete old Pages deployments or not. Default: 'false'

### Jobs

1. **Setup**
   - Caches or downloads the Godot engine and export templates based on the provided `godot-version`.

2. **Parse**
   - Parses the `export_presets.cfg` file to generate a matrix of platforms and default export paths.

3. **Build**
   - Builds the project for each platform defined in the matrix.
   - Exports to the appropriate directories with the platform-specific filenames and extensions.
   - Uploads the artifacts for each platform, including a special step for Web exports.

5. **Deploy** (Conditional)
   - Cleans up old GitHub Pages deployments if the Web export is included.
   - Deploys the Web export to GitHub Pages if available.

## Usage

To use the workflow, call it from another workflow using (for example) the `workflow_call` event and provide the required inputs:

```yaml
name: Gobot
on:
  workflow_dispatch:
    inputs:
      godot-version:
        description: Godot Version
        default: '4.3'
      delete-old-pages:
        description: Delete Old Pages
        default: 'false'
permissions:
  pages: write
  id-token: write
  deployments: write
jobs:
  Gobot:
    uses: rakkarage/auto/.github/workflows/gobot.yml@main
    with:
      godot-version: ${{ github.event.inputs.godot-version }}
      delete-old-pages: ${{ github.event.inputs.delete-old-pages }}
```

## Todo

- gen win icon with `rcedit`?
- cache project import files in .godot folder?
