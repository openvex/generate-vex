# OpenVEX Generation GitHub Action

This repository contains a GitHub action that enables the automatic generation
of VEX (Vulnerability Eploitability Exchange) data using the OpenVEX tooling.

## Usage

To start generating openvex data you need to create your VEX feed and generate
OpenVEX data when you cut a release.

### Initialize your OpenVEX Feed

To generate VEX data, generate golden VEX data in for your project. The easiest
way to do it is to download [`vexctl`](https://github.com/openvex/vexctl/releases/latest),
initialize the OpenVEX directory and commit the result:

```bash
cd myproject
vexctl generate --init
git add .openvex
git commit -sm "initialize OpenVEX Feed"
```

After pushing the commit, your VEX feed is ready to go! :rocket:

### Generating OpenVEX data at Release Time

To generate OpenVEX data when cutting a release, add a new workflow to your
repository to create the document from the templates you just created. Here is
an example:

```yaml
on:
  workflow_dispatch:
  push:
    tags:
      - 'v*.*.*'
jobs:
  vexctl:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2
      - name: Set env
        run: echo "RELEASE_VERSION=${GITHUB_REF#refs/*/}" >> $GITHUB_ENV
      - uses: openvex/generate-vex@31b415924ea0d72ed5f2640f1dee59dea6c2770b
        name: Run vexctl
        with:
            product: pkg:generic/hello@${{ env.RELEASE_VERSION }}


```

This example workflow generates an openvex document and outputs it to
the actions log. You can publish the document with your artifacts or attach
it to container images.
