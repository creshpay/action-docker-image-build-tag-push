# Action-docker-image-build-tag-push
This action can build, tag and push a docker image to a registry (ghcr.io by defaults).

## Requirement

Your have to checkout your project containing the Dockerfile before running the step

## Usage

See [action.yml](action.yml)

Basic:

```yaml
steps:
- name: Checkout repository
  uses: actions/checkout@v2
  with:
    token: ${{ secrets.GITHUB_TOKEN }}

- name: Build, tag, push
  uses: cresh-io/action-docker-image-build-tag-push@v1
  with:
    registry: "${{ env.REGISTRY }}"
    username: "${{ github.actor }}"
    password: "${{ secrets.GITHUB_TOKEN }}"
    image-name: "${{ github.repository }}"
    github-sha: "${{ github.sha }}"
    action: "${{ github.event.action }}"
    merged: "${{ github.event.pull_request.merged }}"
    tag-prefix: "api-"
    tag-suffix: "-rc0"
    push: "false"
    version-pattern: "my-awesome-app-v"
    build-args: |
      NPM_TOKEN=${{ secrets.CI_NPM_TOKEN }}
```

### Parameters

* **registry** - optional

  Registry where you want to push your image, default to ghcr.io

* **username** - required

  Username which have permission to push image. If you use ghcr within the same repo you can set it to `${{ github.actor }}`

* **password** - required

  Password to authenticate the user which have permission to push image. If you use ghcr within the same repo you can set it to `${{ secrets.GITHUB_TOKEN }}`

* **image-name** - required

  Name of the image, to push in the ghcr of the same repo you can set it to `${{ github.repository }}`

* **github-sha** - required

  Used for intelligent caching, set it to the `${{ github.sha }}` value

* **action** - required

  Used to intelligently define tags, set it to the `${{ github.event.action }}` value

* **merged** - required

  Used to intelligently define tags, set it to the `${{ github.event.pull_request.merged }}` value

* **build-args** - required

  Docker build arguments

* **context** - optional - default = .

  Docker build context (path)

* **cache-type** - optional - default = local

  Docker cache type, either inline or local or registry or gha

* **tag-prefix** - optional - default = ""

  Tag prefix (eg. when using monorepo)

* **tag-suffix** - optional - default = ""

  Tag suffix (eg. when using monorepo)

* **push** - optional - default = "true"

  Used to avoid push if needed

* **version-pattern** - optional - default = ""

  Match version pattern to find semver version in prefixed tags. For instance if your tags are like `my-awesome-app-v1.0.0` set `version-pattern` to `my-awesome-app-v` to catch tags `1`, `1.0` and `1.0.1`.

  You can also use regex like `my-.*-v`.

## Outputs

The tags attached to the build will be output for use later. For instance:

```yml
- name: Build, tag, push
  id: btp
  uses: cresh-io/action-docker-image-build-tag-push@v1
  with:
    registry: "${{ env.REGISTRY }}"
    username: "${{ github.actor }}"
    password: "${{ secrets.GITHUB_TOKEN }}"
    image-name: "${{ github.repository }}"
    github-sha: "${{ github.sha }}"
    action: "${{ github.event.action }}"
    merged: "${{ github.event.pull_request.merged }}"
    build-args: |
      NPM_TOKEN=${{ secrets.CI_NPM_TOKEN }}

- name: debug
  run: echo ${{ steps.btp.outputs.tags }}

```

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE)