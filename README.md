# Cloud Foundry CLI Plugin Repository (CLIPR)[![Build Status](https://travis-ci.org/cloudfoundry-incubator/cli-plugin-repo.svg?branch=master)](https://travis-ci.org/cloudfoundry-incubator/cli-plugin-repo)

This is a public repository for community created CF CLI plugins. To submit your plugin
approval, please submit a pull request according to the guidelines below.

## Submitting Plugins

1. You need to have [git](https://git-scm.com/downloads) installed
1. Clone this repo `git clone https://github.com/cloudfoundry-incubator/cli-plugin-repo`
1. Include your plugin information in `repo-index.yml`, here is an example of a new plugin entry
  ```yaml
  - name: new_plugin
    description: new_plugin to be made available for the CF community
    version: 1.0.0
    created: 2015-01-31T00:00:00Z
    updated: 2015-01-31T00:00:00Z
    company:
    authors:
    - name: Sample-Author
      homepage: https://github.com/sample-author
      contact: contact@sample-author.io
    homepage: https://github.com/sample-author/new_plugin
    binaries:
    - platform: osx
      url: https://github.com/sample-author/new_plugin/releases/download/v1.0.0/echo_darwin
      checksum: 2a087d5cddcfb057fbda91e611c33f46
    - platform: win64
      url: https://github.com/sample-author/new_plugin/releases/download/v1.0.0/echo_win64.exe
      checksum: b4550d6594a3358563b9dcb81e40fd66
    - platform: linux32
      url: https://github.com/sample-author/new_plugin/releases/download/v1.0.0/echo_linux32
      checksum: f6540d6594a9684563b9lfa81e23id93
  ```
  Please make sure the spacing and colons are correct in the entry. The following descibes each field's usage.

  Field | Description
  ------ | ---------
  `name` | Name of your plugin, must not conflict with other existing plugins in the repo.
  `description` | Describe your plugin in a line or two. This desscription will show up when your plugin is listed on the command line
  `version` | Version number of your plugin, in [major].[minor].[build] form
  `created` | Date of first submission of the plugin, in [ISO 8601 Combined Date and Time with Timezone Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)
  `updated` | Date of last update of the plugin, in [ISO 8601 Combined Date and Time with Timezone Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)
  `company` | <b>Optional</b> field detailing company or organization that created the plugin
  `authors` | Fields to detail the authors of the plugin<br>`name`: name of author<br>`homepage`: <b>Optional</b> link to the homepage of the author<br>`contact`: <b>Optional</b> ways to contact author, email, twitter, phone etc ...
  `homepage` | Link to the homepage where the source code is hosted. Currently we only support open source plugins
  `binaries` | This section has fields detailing the various binary versions of your plugin. To reach as large an audience as possible, we encourage contributors to cross-compile their plugins on as many platforms as possible. Go provides everything you need to cross-compile for different platforms<br>`platform`: The os for this binary. Supports `osx`, `linux32`, `linux64`, `win32`, `win64`<br>`url`: HTTPS link to the binary file itself<br>`checksum`: SHA-1 of the binary file for verification

1. After making the changes, fork the repository
1. Add your fork as a remote
   ```
   cd $GOPATH/src/github.com/cloudfoundry-incubator/cli-plugin-repo
   git remote add your_name https://github.com/your_name/cli-plugin-repo
   ```

1. Push the changes to your fork and submit a Pull Request

## Releasing Plugins

### Cross-compile to the 3 different operating systems

Golang supports cross compilation to several systems and architectures. Theres an in-depth article by Dave Cheney [here](http://dave.cheney.net/2015/08/22/cross-compilation-with-go-1-5) explaining how to do it and how it works. You can also find a list of supported systems and architectures [here](https://golang.org/doc/install/source#environment) under the `$GOOS and $GOARCH` section.

The CF cli supports 5 combinations:
* `linux`/`386` (known as `linux32`)
* `linux`/`amd64` (known as `linux64`)
* `windows`/`386` (known as `win32`)
* `windows`/`amd64` (known as `win64`)
* `darwin `/`amd64` (known as `osx`)

And at a minimum we want plugins to support `linux64`, `win64` and `osx`.

So, with all that, you can generate those binaries for your plugin with the following snippet:

```bash
PLUGIN_PATH=$GOPATH/src/my-plugin
PLUGIN_NAME=$(basename $PLUGIN_PATH)

cd $PLUGIN_PATH
GOOS=linux GOARCH=amd64 go build -o ${PLUGIN_NAME}.linux64
GOOS=linux GOARCH=386 go build -o ${PLUGIN_NAME}.linux32
GOOS=windows GOARCH=amd64 go build -o ${PLUGIN_NAME}.win64
GOOS=windows GOARCH=386 go build -o ${PLUGIN_NAME}.win32
GOOS=darwin GOARCH=amd64 go build -o ${PLUGIN_NAME}.osx
```

### Checksums

Checksums in the `repo-index.yml` file are used to verify the integrity of the binaries, to prevent corrupted downloads from being installed. We use the [`sha-1`](https://en.wikipedia.org/wiki/SHA-1) checksum algorithm, you can compute it with: `shasum -a 1 <myfile>`

So continuing the above snipped you'd do:

```bash
shasum -a 1 ${PLUGIN_NAME}.linux64
shasum -a 1 ${PLUGIN_NAME}.linux32
shasum -a 1 ${PLUGIN_NAME}.win64
shasum -a 1 ${PLUGIN_NAME}.win32
shasum -a 1 ${PLUGIN_NAME}.osx
```

Take note of those so that you can put them on `repo-index.yml` later when you have uploaded the binaries.

### Release the binary publicly

You could use whatever file hosting you like here, the easiest and recommended one is GitHub releases, given that your plugin's code is already hosted on GitHub it might be the easiest solution too.

You can read more about GitHub Releases [here](https://help.github.com/articles/creating-releases/) but for the purposes of releasing your plugin you should upload those five binaries generated above on the same release.

You should then copy the resulting links for the uploaded binaries from the release page and put them on the `repo-index.yml` file.

This process can get a little tedious if you do it manually every time, that's why some plugin developers have automated it. You can probably put together scripts based on the snippets above to automate compiling, generating checksums and uploading the release to GitHub. There are tools available to manage GitHub releases such as [this one](https://github.com/aktau/github-release).


## Running your own Plugin Repo Server

Included as part of this repository is the CLI Plugin Repo (CLIPR), a reference implementation of a repo server. For information on how to run CLIPR or how to write your own, [please see the CLIPR documentation here.](https://github.com/cloudfoundry-incubator/cli-plugin-repo/blob/master/docs/CLIPR.md)
