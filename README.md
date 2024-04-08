# thunderstore-publish

Github Action that uses the Thunderstore CLI to upload a package to Thunderstore

## Usage

```yml
name: Publish Mod

# Run when a new release is... released
on: 
  release:
    types: [published]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      # Use checkout to publish the files in your repo
      - uses: actions/checkout@v3
      - uses: dhkatz/thunderstore-publish@v1.0.0
        with:
          namespace: dhkatz # the thunderstore 'team' to publish under
          description: Test 
          token: ${{ secrets.YOUR_TOKEN_NAME }}
          name: test # the name of the package
          version: ${{ github.ref_name }} # Use the tag as the package version
          community: Northstar
          categories: | # <-- notice this pipe character
            foo
            bar-baz

```

## Categories

Figuring out how to refer to your community's categories can be somewhat confusing. 

To find the categories available to your community, check out `https://thunderstore.io/api/experimental/community/YOUR_COMMUNITY/category/`, which will return a list of categories in a JSON format. Each category will have a `name` value and a `slug` value. The `slug` is the value you need to put in the `categories` list of the action.

### Example
 
```bash
curl -X GET "https://thunderstore.io/api/experimental/community/northstar/category/" -H  "accept: application/json" | jq # 'jq' is a command line utility that formats JSON
```
```json5
{
  "pagination": {
    "next_link": null,
    "previous_link": null
  },
  "results": [
    {
      "name": "Models",
      "slug": "models"
    },
    {
      "name": "Maps",
      "slug": "maps"
    },
    // a bunch of others...
  ]
}
```

## Inputs

| Input         | Description                                                                                                                                                         | Required |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| `token`       | Service account token from Thunderstore. Should be saved as a repo secret and accessed with `${{ secrets.YOUR_TOKEN_NAME }}`                                        | `true`   |
| `community`   | Thunderstore community to publish to.                                                                                                                               | `true`   |
| `namespace`   | Name of the team to publish under.                                                                                                                                  | `true`   |
| `repo`        | URL or hostname of the repository to publish to (see [#22](https://github.com/GreenTF/upload-thunderstore-package/issues/22)).                                      | `false`  |
| `name`        | Name of the package.                                                                                                                                                | `true`   |
| `description` | Description of the package that will appear on Thunderstore.                                                                                                        | `true`   |
| `version`     | Package version in SemVer format.                                                                                                                                   | `true`   |
| `file`        | Path to a prebuilt zip file. Will skip the build step if provided.                                                                                                  | `false`  |
| `path`        | Path of the files to package. Useful when using build artifacts from other steps. Defaults to using the contents of the repo.                                       | `false`  |
| `icon`        | URL to download the icon from. Will try to find `icon.png` in the root of the repo if not provided.                                                                 | `false`  |
| `readme`      | URL to download the readme from. Will try to find `README.md` in the root of the repo if not provided.                                                              | `false`  |
| `dev`         | Publish to https://thunderstore.dev if set, https://thunderstore.io if not set.                                                                                     | `false`  |
| `categories`  | A list, separated by newline characters, of categories to give to the mod when published. These must be available in the community you're publishing to.            | `false`  |
| `deps`        | A list, separated by spaces, of mods this mod depends on. Must be in `namespace-modname@1.2.3` format. The publish will fail if any of these aren't a real package. | `false`  |
| `website`     | The homepage URL for the mod. Defaults to the github repo URL.                                                                                                      | `false`  |
| `nsfw`        | Set this to `true` mark the mod as NSFW                                                                                                                             | `false`  | 

## Outputs

| Output | Description                               |
|--------|-------------------------------------------|
| `url`  | The download URL of the published package |
