PAYMENT ADVICE
To: Zachry Tyler Wood
5323 BRADFORD DR
DALLAS TX 75235-8313
UNITED STATES
Customer Internal Revenue Service (IRS) - Internal
Revenue Service - 1111 Constitution Ave.,
N.W. Washinton, D.C. 20535
Overdue
0.00
Current
(283,370,975,464.00
)
Total USD Due
(283,370,975,464.00
)
Amount Enclosed
Enter the amount you are paying above
Company Registration No  CIK0000835271.   Registered Office ::'
Skip to content
Search or jump to…
Pull requests
Issues
Marketplace
Explore
 
@zakwarlord7 
Your account has been flagged.
Because of that, your profile is hidden from the public. If you believe this is a mistake, contact support to have your account status reviewed.
zakwarlord7
/
INTERNAL
Public
generated from zakwarlord7/exploreer
Code
Issues
Pull requests
Actions
Projects
Wiki
Security
Insights
Settings
Comparing changes
Choose two branches to see what’s changed or to start a new pull request. If you need to, you can also .
  
There isn’t anything to compare.
main and patch-1 are entirely different commit histories.

Showing 1,403 changed files with 13,643 additions and 0 deletions.
 44  
.github/PULL_REQUEST_TEMPLATE.md
@@ -0,0 +1,44 @@
<!-- Thank you for contributing! -->
### Please confirm this pull request meets the following requirements:

- [ ] I followed the contributing guidelines: <https://github.com/github/explore/blob/main/CONTRIBUTING.md>.
- [ ] I have no affiliation with the project I am suggesting (as a maintainer, creator, contractor, or employee).

### Which change are you proposing?

  - [ ] Suggesting edits to an existing topic or collection
  - [ ] Curating a new topic or collection
  - [ ] Something that does not neatly fit into the binary options above

---

<!-- ⚠️ Please select either this section... ⚠️ -->
### Editing an existing topic or collection

I'm suggesting these edits to an existing topic or collection:
- [ ] Image (and my file is `*.png`, square, dimensions 288x288, size <= 75 kB)
- [ ] Content (and my changes are in `index.md`)

> Please replace this line with an explanation of why you think these changes should be made.
<!-- ⚠️ ... or this section ⚠️ -->
### Curating a new topic or collection

- [ ] I've formatted my changes as a new folder directory, named for the topic or collection as it appears in the URL on GitHub (e.g. `https://github.com/topics/[NAME]` or `https://github.com/collections/[NAME]`)
- [ ] My folder contains a `*.png` image (if applicable) and `index.md`
- [ ] All required fields in my `index.md` conform to the Style Guide and API docs: <https://github.com/github/explore/tree/main/docs>

> Please replace this line with an explanation of why you think this topic or collection should be curated.
<!-- ⚠️ ... or this section ⚠️ -->
### Something that does not neatly fit into the binary options above

- [ ] My suggested edits are not about an existing topic or collection, or at least not a single one
- [ ] My suggested edits are not about curating a new topic or collection, or at least not a single one
- [ ] My suggested edits conform to the Style Guide and API docs: https://github.com/github/explore/tree/main/docs

> Please replace this line with an explanation of your proposed changes.
---

**Please note: we will close your PR without comment if you do not check the boxes above and provide ALL requested information.**
 12  
.github/dependabot.yml
@@ -0,0 +1,12 @@
version: 2
updates:
  - package-ecosystem: github-actions
    directory: "/"
    schedule:
      interval: daily
      time: "10:00"
      timezone: Europe/Vienna
    pull-request-branch-name:
      separator: "-"
    open-pull-requests-limit: 99
    rebase-strategy: disabled
 31  
.github/workflows/collections-renames.yml
@@ -0,0 +1,31 @@
name: Check and update renamed/removed collection items

on:
  workflow_dispatch:
  schedule:
    - cron:  "0 * * * *" # every hour

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          bundler-cache: true

      - name: Test collection with autofix and commit changes
        uses: technote-space/create-pr-action@v2
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          AUTOCORRECT_RENAMED_REPOS: 1
        with:
          EXECUTE_COMMANDS: |
            bundle exec rake collections
          COMMIT_MESSAGE: '✨ Autofixing renamed/removed collection items ✨'
          COMMIT_NAME: 'GitHub Actions'
          COMMIT_EMAIL: 'actions@github.com'
          PR_BRANCH_NAME: 'update-collections-${PR_ID}'
          PR_TITLE: '✨ Autofixing renamed/removed collection items ✨'
 28  
.github/workflows/conflict.yml
@@ -0,0 +1,28 @@
name: Check for conflicts of interest

on:
  pull_request:
    types:
      - opened
      - reopened
      - ready_for_review
      - edited

jobs:
  fail:
    runs-on: ubuntu-latest
    if: contains(github.event.pull_request.body, '- [ ] I have no affiliation with the project I am suggesting (as a maintainer, creator, contractor, or employee).')
    steps:
      - run: |
          echo "In order to review this pull request for acceptance, we need to make sure that all of the prerequisites are satisfied."
          echo "This was not checked:"
          echo "> I have no affiliation with the project I am suggesting (as a maintainer, creator, contractor, or employee)"
          echo "This is a requirement to maintain a high level of independence in this project. Please update if you are able to verify that you meet that requirement."
          echo "Thank you!"
          exit 1
  succeed:
    runs-on: ubuntu-latest
    if: contains(github.event.pull_request.body, '- [x] I have no affiliation with the project I am suggesting (as a maintainer, creator, contractor, or employee).')
    steps:
      - run: exit 0
 30  
.github/workflows/jekyll_build.yml
@@ -0,0 +1,30 @@
name: Build and Publish Jekyll for GitHub Pages

on:
  push:
    branches:
      - main

jobs:
  jekyll:
    runs-on: ubuntu-latest
    steps:
    - name: 📂 setup
      uses: actions/checkout@v2

    - name: 💎 setup ruby
      uses: ruby/setup-ruby@v1
      with:
        ruby-version: 3.1.2

    - name: 🔨 install dependencies & build site
      uses: limjh16/jekyll-action-ts@v2
      with:
        enable_cache: true

    - name: 🚀 deploy
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./_site
        publish_branch: gh-pages
 25  
.github/workflows/lint.yml
@@ -0,0 +1,25 @@
name: Lint

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          bundler-cache: true

      - name: Run linters
        uses: wearerequired/lint-action@v2
        with:
          auto_fix: ${{ github.event_name != 'pull_request' || github.event.pull_request.head.repo.owner.login == github.repository_owner }}
          rubocop: true
          rubocop_command_prefix: bundle exec
 28  
.github/workflows/stale.yml
@@ -0,0 +1,28 @@
name: Mark stale PRs
on:
  workflow_dispatch:
  schedule:
  - cron: "0 12 * * *"

permissions:
  contents: read

jobs:
  stale:
    permissions:
      issues: write  # for actions/stale to close stale issues
      pull-requests: write  # for actions/stale to close stale PRs
    runs-on: ubuntu-latest
    steps:
    - uses: actions/stale@v5
      with:
        stale-pr-message: >
          This pull request has been automatically marked as stale because it has not
          had recent activity. It will be closed if no further activity occurs.
          Thank you for your contributions.
        stale-pr-label: "stale"
        exempt-pr-labels: "pinned,security,dependencies"
        days-before-pr-stale: 30
        days-before-pr-close: 7
        ascending: true
        operations-per-run: 100
 29  
.github/workflows/test.yml
@@ -0,0 +1,29 @@
name: Test

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  test:
    strategy:
      fail-fast: false
      matrix:
        test_type:
          - collections
          - topics
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          bundler-cache: true

      - name: Build and test with Rake
        run: bundle exec rake ${{ matrix.test_type }}
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
 8  
.gitignore
@@ -0,0 +1,8 @@
.DS_Store
scripts/
_site
.sass-cache
.jekyll-cache
.jekyll-metadata
vendor
.bundle
 34  
.rubocop.yml
@@ -0,0 +1,34 @@
require: rubocop-performance

AllCops:
  TargetRubyVersion: 3.1
  Exclude:
    - '**/bin/**/*'
    - '**/db/**/*'
    - '**/script/setup'
    - '**/vendor/**/*'

Lint/AssignmentInCondition:
  Enabled: false

Metrics/BlockLength:
  Enabled: false

Layout/LineLength:
  Max: 100
  AllowedPatterns: ['\A\s*#']

Naming/RescuedExceptionsVariableName:
  PreferredName: "error"

Style/FrozenStringLiteralComment:
  Enabled: false

Style/StringLiterals:
  EnforcedStyle: double_quotes

Style/TrailingCommaInArrayLiteral:
  EnforcedStyleForMultiline: consistent_comma

Style/TrailingCommaInHashLiteral:
  EnforcedStyleForMultiline: consistent_comma
 1  
.ruby-version
@@ -0,0 +1 @@
3.1.2
 1  
CNAME
@@ -0,0 +1 @@
explore-feed.github.com
 2  
CODEOWNERS
This CODEOWNERS file contains errors …
@@ -0,0 +1,2 @@
*  @github/communities-oss-reviewers
collections/made-in-india/ @github/india-community-reviewers @github/communities-oss-reviewers
 46  
CODE_OF_CONDUCT.md
@@ -0,0 +1,46 @@
# Contributor Covenant Code of Conduct

## Our Pledge

In the interest of fostering an open and welcoming environment, we as contributors and maintainers pledge to making participation in our project and our community a harassment-free experience for everyone, regardless of age, body size, disability, ethnicity, gender identity and expression, level of experience, nationality, personal appearance, race, religion, or sexual identity and orientation.

## Our Standards

Examples of behavior that contributes to creating a positive environment include:

* Using welcoming and inclusive language
* Being respectful of differing viewpoints and experiences
* Gracefully accepting constructive criticism
* Focusing on what is best for the community
* Showing empathy towards other community members

Examples of unacceptable behavior by participants include:

* The use of sexualized language or imagery and unwelcome sexual attention or advances
* Trolling, insulting/derogatory comments, and personal or political attacks
* Public or private harassment
* Publishing others' private information, such as a physical or electronic address, without explicit permission
* Other conduct which could reasonably be considered inappropriate in a professional setting

## Our Responsibilities

Project maintainers are responsible for clarifying the standards of acceptable behavior and are expected to take appropriate and fair corrective action in response to any instances of unacceptable behavior.

Project maintainers have the right and responsibility to remove, edit, or reject comments, commits, code, wiki edits, issues, and other contributions that are not aligned to this Code of Conduct, or to ban temporarily or permanently any contributor for other behaviors that they deem inappropriate, threatening, offensive, or harmful.

## Scope

This Code of Conduct applies both within project spaces and in public spaces when an individual is representing the project or its community. Examples of representing a project or community include using an official project e-mail address, posting via an official social media account, or acting as an appointed representative at an online or offline event. Representation of a project may be further defined and clarified by project maintainers.

## Enforcement

Instances of abusive, harassing, or otherwise unacceptable behavior may be reported to [opensource@github.com](mailto:opensource@github.com). All complaints will be reviewed and investigated and will result in a response that is deemed necessary and appropriate to the circumstances. The project team is obligated to maintain confidentiality with regard to the reporter of an incident. Further details of specific enforcement policies may be posted separately.

Project maintainers who do not follow or enforce the Code of Conduct in good faith may face temporary or permanent repercussions as determined by other members of the project's leadership.

## Attribution

This Code of Conduct is adapted from the [Contributor Covenant][homepage], version 1.4, available at [http://contributor-covenant.org/version/1/4][version]

[homepage]: http://contributor-covenant.org
[version]: http://contributor-covenant.org/version/1/4/
 167  
CONTRIBUTING.md
@@ -0,0 +1,167 @@
{
  "name": "create-pull-request",
  "version": "3.0.0",
  "private": true,
  "description": "Creates a pull request for changes to your repository in the actions workspace",
  "main": "lib/main.js",
  "scripts": {
    "build": "tsc && ncc build",
    "format": "prettier --write '**/*.ts'",
    "format-check": "prettier --check '**/*.ts'",
    "lint": "eslint src/**/*.ts",
    "test:unit": "jest unit",
    "test:int": "__test__/integration-tests.sh",
    "test": "npm run test:unit && npm run test:int"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/peter-evans/create-pull-request.git"
  },
  "keywords": [
    "actions",
    "pull",
    "request"
  ],
  "author": "Peter Evans",
  "license": "MIT",
  "bugs": {
    "url": "https://github.com/peter-evans/create-pull-request/issues"
  },
  "homepage": "https://github.com/peter-evans/create-pull-request",
  "dependencies": {
    "@actions/core": "1.2.6",
    "@actions/exec": "1.0.4",
    "@octokit/core": "3.2.4",
    "@octokit/plugin-paginate-rest": "2.8.0",
    "@octokit/plugin-rest-endpoint-methods": "4.5.2",
    "uuid": "8.3.2"
  },
  "devDependencies": {
    "@types/jest": "26.0.20",
    "@types/node": "14.14.22",
    "@typescript-eslint/parser": "4.14.0",
    "@vercel/ncc": "0.27.0",
    "eslint": "7.18.0",
    "eslint-plugin-github": "4.1.1",
    "eslint-plugin-jest": "24.1.3",
    "jest": "26.6.3",
    "jest-circus": "26.6.3",
    "js-yaml": "4.0.0",
    "prettier": "2.2.1",
    "ts-jest": "26.4.4",
    "typescript": "4.1.3"
  }
}
# Contributing

Hi there! We're excited you've got ideas to improve topics and collections. You're helping the community discover valuable information.

This project adheres to the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

There are a few ways you can contribute:

- Improving an existing topic or collection
- Curating a new topic or collection

As you write content, check out the [Style Guide](./docs/styleguide.md) to learn what each field means, and how it should be formatted. Following the style guide will increase the chances of your contribution being accepted.

Notes: 
  - Updates won't immediately appear once we've merged your PR. We pull in these changes regularly to GitHub.
  - Please limit your pull request to the creation/updating of one topic or collection at a time.

## Improving an existing topic or collection

If a topic or collection already exists, it will be listed in its respective directory:

- [topics/](https://github.com/github/explore/tree/main/topics)
- [collections/](https://github.com/github/explore/tree/main/collections)

The topic or collection name should match its URL, e.g. `https://github.com/topics/rails` corresponds to the [`topics/rails` directory](https://github.com/github/explore/tree/main/topics/rails).

To make an improvement, please **open a pull request** with your proposed changes:

### Update the image

To update the image, simply replace the image inside the directory for the topic or collection.

### Update text and links

To update text and links, edit the `index.md` inside the topic or collection's directory. These files are formatted using a combination of [Front Matter](https://jekyllrb.com/docs/frontmatter/) and simple body content.

For **topics**, you'll notice that, in examples like the topic "[algorithm](https://raw.githubusercontent.com/github/explore/main/topics/algorithm/index.md)," data like its canonical URL, Wikipedia URL, or display name are called out in key-value pairs, while its detailed description is accounted for in the body of the document.

_/topics/algorithm/index.md_:
```
---
aliases: algorithms
display_name: Algorithm
short_description: Algorithms are self-contained sequences that carry out a variety of tasks.
topic: algorithm
wikipedia_url: https://en.wikipedia.org/wiki/Algorithm
---
Algorithms are detailed sets of guidelines created for a computer program to complete tasks efficiently and thoroughly.
```

---

Similarly, **collections** like "[music](https://raw.githubusercontent.com/github/explore/main/collections/music/index.md)" call out things like their author and display name in Front Matter variables -- with a detailed description in the body of the document. Most importantly, though, collections identify their individual collection items in [a YAML list](https://en.wikipedia.org/wiki/YAML#Basic_components) for the key "items."

_/collections/music/index.md_:

```
---
items:
 - beetbox/beets
 - scottschiller/SoundManager2
 - CreateJS/SoundJS
 - musescore/MuseScore
 - tomahawk-player/tomahawk
 - cashmusic/platform
 - mopidy/mopidy
 - AudioKit/AudioKit
 - Soundnode/soundnode-app
 - gillesdemey/Cumulus
 - metabrainz/picard
 - overtone/overtone
 - samaaron/sonic-pi
display_name: Music
created_by: jonrohan
---
Drop the code bass with these musically themed repositories.
```

---

The [pull request template](./.github/PULL_REQUEST_TEMPLATE.md) also provides guidance on the information you need to include.

**Please fill out the pull request template completely.** If you do not fill out the template, your PR will be closed.

## Curating a new topic or collection

If a topic or collection is not yet curated, it will NOT be listed in its respective directory.

We are likely to consider suggestions to curate a topic or collection that is valuable to GitHub's community. Valuable topics, for example, include those that are already [widely used by repositories](https://help.github.com/articles/classifying-your-repository-with-topics/) and could benefit from the addition of important information. When suggesting content, please consider how to make your contribution broadly useful and relevant to others, rather than serving a specific use case.

Please note that all suggestions must adhere to GitHub's [Community Guidelines](https://help.github.com/articles/github-community-guidelines/) and [Terms of Service](https://help.github.com/articles/github-terms-of-service/). Per our Terms of Service, [you are responsible](https://help.github.com/articles/github-terms-of-service/#d-user-generated-content) for the content you contribute, and you must have the rights to use it.

To propose a new topic or collection, please **open a pull request** with your proposed additions. The [API docs](./docs/API.md) and [style guide](./docs/styleguide.md) provide guidance on the information you need to include and how it should be formatted.

This repository includes [a list of the most-used GitHub topics that don't yet have extra context](topics-todo.md). If your pull request adds one of these topics, please update topics-todo.md so that the topic is checked (marked complete).

**Please fill out the pull request template completely.** If you do not fill out the template, your pull request will be closed.

## Guidelines

* Avoid conflicts of interest. Maintainers of a project cannot add a topic or collection for their own project. If a topic is popular enough to warrant inclusion, someone else will add or improve it.

## Running tests

There are some lint tests in place to ensure each topic is formatted in the way we expect. Travis
CI will run the tests automatically. If you want to run the tests yourself locally, you will need
Ruby and Bundler installed.

You can run the tests using:

```bash
script/cibuild
```
 19  
Gemfile
@@ -0,0 +1,19 @@
source "https://rubygems.org"

gem "github-pages", group: :jekyll_plugins

group :test do
  gem "fastimage"
  gem "httparty"
  gem "minitest"
  gem "octokit"
  gem "pry", require: false
  gem "rake"
  gem "rubocop"
  gem "rubocop-performance"
  gem "safe_yaml"
end

group :development do
  gem "webrick"
end
 330  
Gemfile.lock
@@ -0,0 +1,330 @@
GEM
  remote: https://rubygems.org/
  specs:
    activesupport (6.0.4.7)
      concurrent-ruby (~> 1.0, >= 1.0.2)
      i18n (>= 0.7, < 2)
      minitest (~> 5.1)
      tzinfo (~> 1.1)
      zeitwerk (~> 2.2, >= 2.2.2)
    addressable (2.8.0)
      public_suffix (>= 2.0.2, < 5.0)
    ast (2.4.2)
    coderay (1.1.3)
    coffee-script (2.4.1)
      coffee-script-source
      execjs
    coffee-script-source (1.11.1)
    colorator (1.1.0)
    commonmarker (0.23.4)
    concurrent-ruby (1.1.10)
    dnsruby (1.61.9)
      simpleidn (~> 0.1)
    em-websocket (0.5.3)
      eventmachine (>= 0.12.9)
      http_parser.rb (~> 0)
    ethon (0.15.0)
      ffi (>= 1.15.0)
    eventmachine (1.2.7)
    execjs (2.8.1)
    faraday (1.10.0)
      faraday-em_http (~> 1.0)
      faraday-em_synchrony (~> 1.0)
      faraday-excon (~> 1.1)
      faraday-httpclient (~> 1.0)
      faraday-multipart (~> 1.0)
      faraday-net_http (~> 1.0)
      faraday-net_http_persistent (~> 1.0)
      faraday-patron (~> 1.0)
      faraday-rack (~> 1.0)
      faraday-retry (~> 1.0)
      ruby2_keywords (>= 0.0.4)
    faraday-em_http (1.0.0)
    faraday-em_synchrony (1.0.0)
    faraday-excon (1.1.0)
    faraday-httpclient (1.0.1)
    faraday-multipart (1.0.3)
      multipart-post (>= 1.2, < 3)
    faraday-net_http (1.0.1)
    faraday-net_http_persistent (1.2.0)
    faraday-patron (1.0.0)
    faraday-rack (1.0.0)
    faraday-retry (1.0.3)
    fastimage (2.2.6)
    ffi (1.15.5)
    forwardable-extended (2.6.0)
    gemoji (3.0.1)
    github-pages (226)
      github-pages-health-check (= 1.17.9)
      jekyll (= 3.9.2)
      jekyll-avatar (= 0.7.0)
      jekyll-coffeescript (= 1.1.1)
      jekyll-commonmark-ghpages (= 0.2.0)
      jekyll-default-layout (= 0.1.4)
      jekyll-feed (= 0.15.1)
      jekyll-gist (= 1.5.0)
      jekyll-github-metadata (= 2.13.0)
      jekyll-include-cache (= 0.2.1)
      jekyll-mentions (= 1.6.0)
      jekyll-optional-front-matter (= 0.3.2)
      jekyll-paginate (= 1.1.0)
      jekyll-readme-index (= 0.3.0)
      jekyll-redirect-from (= 0.16.0)
      jekyll-relative-links (= 0.6.1)
      jekyll-remote-theme (= 0.4.3)
      jekyll-sass-converter (= 1.5.2)
      jekyll-seo-tag (= 2.8.0)
      jekyll-sitemap (= 1.4.0)
      jekyll-swiss (= 1.0.0)
      jekyll-theme-architect (= 0.2.0)
      jekyll-theme-cayman (= 0.2.0)
      jekyll-theme-dinky (= 0.2.0)
      jekyll-theme-hacker (= 0.2.0)
      jekyll-theme-leap-day (= 0.2.0)
      jekyll-theme-merlot (= 0.2.0)
      jekyll-theme-midnight (= 0.2.0)
      jekyll-theme-minimal (= 0.2.0)
      jekyll-theme-modernist (= 0.2.0)
      jekyll-theme-primer (= 0.6.0)
      jekyll-theme-slate (= 0.2.0)
      jekyll-theme-tactile (= 0.2.0)
      jekyll-theme-time-machine (= 0.2.0)
      jekyll-titles-from-headings (= 0.5.3)
      jemoji (= 0.12.0)
      kramdown (= 2.3.2)
      kramdown-parser-gfm (= 1.1.0)
      liquid (= 4.0.3)
      mercenary (~> 0.3)
      minima (= 2.5.1)
      nokogiri (>= 1.13.4, < 2.0)
      rouge (= 3.26.0)
      terminal-table (~> 1.4)
    github-pages-health-check (1.17.9)
      addressable (~> 2.3)
      dnsruby (~> 1.60)
      octokit (~> 4.0)
      public_suffix (>= 3.0, < 5.0)
      typhoeus (~> 1.3)
    html-pipeline (2.14.1)
      activesupport (>= 2)
      nokogiri (>= 1.4)
    http_parser.rb (0.8.0)
    httparty (0.20.0)
      mime-types (~> 3.0)
      multi_xml (>= 0.5.2)
    i18n (0.9.5)
      concurrent-ruby (~> 1.0)
    jekyll (3.9.2)
      addressable (~> 2.4)
      colorator (~> 1.0)
      em-websocket (~> 0.5)
      i18n (~> 0.7)
      jekyll-sass-converter (~> 1.0)
      jekyll-watch (~> 2.0)
      kramdown (>= 1.17, < 3)
      liquid (~> 4.0)
      mercenary (~> 0.3.3)
      pathutil (~> 0.9)
      rouge (>= 1.7, < 4)
      safe_yaml (~> 1.0)
    jekyll-avatar (0.7.0)
      jekyll (>= 3.0, < 5.0)
    jekyll-coffeescript (1.1.1)
      coffee-script (~> 2.2)
      coffee-script-source (~> 1.11.1)
    jekyll-commonmark (1.4.0)
      commonmarker (~> 0.22)
    jekyll-commonmark-ghpages (0.2.0)
      commonmarker (~> 0.23.4)
      jekyll (~> 3.9.0)
      jekyll-commonmark (~> 1.4.0)
      rouge (>= 2.0, < 4.0)
    jekyll-default-layout (0.1.4)
      jekyll (~> 3.0)
    jekyll-feed (0.15.1)
      jekyll (>= 3.7, < 5.0)
    jekyll-gist (1.5.0)
      octokit (~> 4.2)
    jekyll-github-metadata (2.13.0)
      jekyll (>= 3.4, < 5.0)
      octokit (~> 4.0, != 4.4.0)
    jekyll-include-cache (0.2.1)
      jekyll (>= 3.7, < 5.0)
    jekyll-mentions (1.6.0)
      html-pipeline (~> 2.3)
      jekyll (>= 3.7, < 5.0)
    jekyll-optional-front-matter (0.3.2)
      jekyll (>= 3.0, < 5.0)
    jekyll-paginate (1.1.0)
    jekyll-readme-index (0.3.0)
      jekyll (>= 3.0, < 5.0)
    jekyll-redirect-from (0.16.0)
      jekyll (>= 3.3, < 5.0)
    jekyll-relative-links (0.6.1)
      jekyll (>= 3.3, < 5.0)
    jekyll-remote-theme (0.4.3)
      addressable (~> 2.0)
      jekyll (>= 3.5, < 5.0)
      jekyll-sass-converter (>= 1.0, <= 3.0.0, != 2.0.0)
      rubyzip (>= 1.3.0, < 3.0)
    jekyll-sass-converter (1.5.2)
      sass (~> 3.4)
    jekyll-seo-tag (2.8.0)
      jekyll (>= 3.8, < 5.0)
    jekyll-sitemap (1.4.0)
      jekyll (>= 3.7, < 5.0)
    jekyll-swiss (1.0.0)
    jekyll-theme-architect (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-cayman (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-dinky (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-hacker (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-leap-day (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-merlot (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-midnight (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-minimal (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-modernist (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-primer (0.6.0)
      jekyll (> 3.5, < 5.0)
      jekyll-github-metadata (~> 2.9)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-slate (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-tactile (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-theme-time-machine (0.2.0)
      jekyll (> 3.5, < 5.0)
      jekyll-seo-tag (~> 2.0)
    jekyll-titles-from-headings (0.5.3)
      jekyll (>= 3.3, < 5.0)
    jekyll-watch (2.2.1)
      listen (~> 3.0)
    jemoji (0.12.0)
      gemoji (~> 3.0)
      html-pipeline (~> 2.2)
      jekyll (>= 3.0, < 5.0)
    kramdown (2.3.2)
      rexml
    kramdown-parser-gfm (1.1.0)
      kramdown (~> 2.0)
    liquid (4.0.3)
    listen (3.7.1)
      rb-fsevent (~> 0.10, >= 0.10.3)
      rb-inotify (~> 0.9, >= 0.9.10)
    mercenary (0.3.6)
    method_source (1.0.0)
    mime-types (3.4.1)
      mime-types-data (~> 3.2015)
    mime-types-data (3.2022.0105)
    minima (2.5.1)
      jekyll (>= 3.5, < 5.0)
      jekyll-feed (~> 0.9)
      jekyll-seo-tag (~> 2.1)
    minitest (5.15.0)
    multi_xml (0.6.0)
    multipart-post (2.1.1)
    nokogiri (1.13.7-arm64-darwin)
      racc (~> 1.4)
    nokogiri (1.13.7-x86_64-linux)
      racc (~> 1.4)
    octokit (4.22.0)
      faraday (>= 0.9)
      sawyer (~> 0.8.0, >= 0.5.3)
    parallel (1.22.1)
    parser (3.1.2.0)
      ast (~> 2.4.1)
    pathutil (0.16.2)
      forwardable-extended (~> 2.6)
    pry (0.14.1)
      coderay (~> 1.1)
      method_source (~> 1.0)
    public_suffix (4.0.7)
    racc (1.6.0)
    rainbow (3.1.1)
    rake (13.0.6)
    rb-fsevent (0.11.1)
    rb-inotify (0.10.1)
      ffi (~> 1.0)
    regexp_parser (2.3.0)
    rexml (3.2.5)
    rouge (3.26.0)
    rubocop (1.28.1)
      parallel (~> 1.10)
      parser (>= 3.1.0.0)
      rainbow (>= 2.2.2, < 4.0)
      regexp_parser (>= 1.8, < 3.0)
      rexml
      rubocop-ast (>= 1.17.0, < 2.0)
      ruby-progressbar (~> 1.7)
      unicode-display_width (>= 1.4.0, < 3.0)
    rubocop-ast (1.17.0)
      parser (>= 3.1.1.0)
    rubocop-performance (1.13.3)
      rubocop (>= 1.7.0, < 2.0)
      rubocop-ast (>= 0.4.0)
    ruby-progressbar (1.11.0)
    ruby2_keywords (0.0.5)
    rubyzip (2.3.2)
    safe_yaml (1.0.5)
    sass (3.7.4)
      sass-listen (~> 4.0.0)
    sass-listen (4.0.0)
      rb-fsevent (~> 0.9, >= 0.9.4)
      rb-inotify (~> 0.9, >= 0.9.7)
    sawyer (0.8.2)
      addressable (>= 2.3.5)
      faraday (> 0.8, < 2.0)
    simpleidn (0.2.1)
      unf (~> 0.1.4)
    terminal-table (1.8.0)
      unicode-display_width (~> 1.1, >= 1.1.1)
    thread_safe (0.3.6)
    typhoeus (1.4.0)
      ethon (>= 0.9.0)
    tzinfo (1.2.10)
      thread_safe (~> 0.1)
    unf (0.1.4)
      unf_ext
    unf_ext (0.0.8.1)
    unicode-display_width (1.8.0)
    webrick (1.7.0)
    zeitwerk (2.5.4)

PLATFORMS
  arm64-darwin-21
  x86_64-linux

DEPENDENCIES
  fastimage
  github-pages
  httparty
  minitest
  octokit
  pry
  rake
  rubocop
  rubocop-performance
  safe_yaml
  webrick

BUNDLED WITH
   2.3.12
 395  
LICENSE.txt
Large diffs are not rendered by default.

 23  
README.md
@@ -0,0 +1,23 @@
# GitHub Explore

This repository houses all of the community-curated content for GitHub Topics and Collections.

[Topics](https://help.github.com/articles/about-topics/) help you explore repositories in a particular subject area, learn more about that subject, and find projects to contribute to.

[Collections](https://github.com/collections) help you discover hand-picked repositories, developers, organizations, videos, and articles that share a common theme.

If you want to suggest edits to an existing topic page or collection, or curate a new one, read our [contributing guide](CONTRIBUTING.md) to get started. You can also [review a list of popular topics that need more context](topics-todo.md) to get an idea of where to start.

## Running tests

There are some lint tests in place to ensure each topic is formatted in the way we expect. Travis CI will run the tests automatically. If you want to run the tests yourself locally, you will need Ruby and Bundler installed.

You can run the tests using:

```bash
script/cibuild
```

## Licenses

Content is released under [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/) which gives you permission to use content for almost any purpose (but does not grant you any trademark permissions). See [notices](notices.md) for complete details, including attribution guidelines, contribution terms, and software and third-party licenses and permissions.
 31  
Rakefile
@@ -0,0 +1,31 @@
require "rake/testtask"

Rake::TestTask.new(:topics) do |t|
  t.libs << "test"
  t.test_files = FileList["test/topics_test.rb"]
  t.warning = false
  t.verbose = false
end

Rake::TestTask.new(:collections) do |t|
  t.libs << "test"
  t.test_files = FileList["test/collections_test.rb"]
  t.warning = false
  t.verbose = false
end

Rake::TestTask.new(:default) do |t|
  t.libs << "test"
  t.test_files = FileList["test/*_test.rb"]
  t.warning = false
  t.verbose = false
end

desc "Run topics related tests"
task topics: :test

desc "Run collections related tests"
task collections: :test

desc "Run all tests"
task default: :test
 23  
_config.yml
@@ -0,0 +1,23 @@
title: GitHub Explore Feed
description: Feed for GitHub Topics and Collections.

repository: github/explore

plugins:
  - jekyll-redirect-from

exclude:
  - .git
  - CODEOWNERS
  - Gemfile
  - Gemfile.lock
  - README.md
  - Rakefile
  - collections
  - test
  - topics
  - vendor

collections:
  topics:
  explore_collections:
 1  
_explore_collections
@@ -0,0 +1 @@
collections
 1  
_topics
@@ -0,0 +1 @@
topics
 19  
collections/ai-model-zoos/index.md
@@ -0,0 +1,19 @@
---
items:
 - tensorflow/models
 - Theano/Theano
 - BVLC/caffe
 - facebookarchive/models
 - apache/incubator-mxnet
 - eclipse/deeplearning4j
 - sdhnshu/Pytorch-Model-Zoo
 - Lasagne/Recipes
 - albertomontesg/keras-model-zoo
 - hindupuravinash/the-gan-zoo
 - likedan/Awesome-CoreML-Models
 - microsoft/CNTK
 - github/explore
display_name: Model Zoos of machine and deep learning technologies
created_by: alanbraz
---
Model Zoo is a common way that open source frameworks and companies organize their machine learning and deep learning models.
 12  
collections/cheatsheets/index.md
@@ -0,0 +1,12 @@
---
items:
 - LeCoupa/awesome-cheatsheets
 - detailyang/awesome-cheatsheet
 - FavioVazquez/ds-cheatsheets
 - gto76/python-cheatsheet
 - wilfredinni/python-cheatsheet
 - ihebski/DefaultCreds-cheat-sheet
display_name: Useful cheatsheets
created_by: Luois45
---
A list of useful cheatsheets for various programming languages
 13  
collections/choosing-projects/index.md
@@ -0,0 +1,13 @@
---
items:
 - rust-lang/rust
 - HospitalRun/hospitalrun-frontend
 - Homebrew/brew
 - https://www.youtube.com/embed/dSl_qnWO104
 - public-apis/public-apis
 - C#
 - C++
display_name: How to choose (and contribute to) your first open source project
created_by: kytrinyx
---
New to open source? Here’s how to find projects that need help and start making impactful contributions.
 35  
collections/clean-code-linters/index.md
@@ -0,0 +1,35 @@
---
items:
 - standard/standard
 - eslint/eslint
 - jshint/jshint
 - clutchski/coffeelint
 - csscomb/csscomb.js
 - sds/scss-lint
 - htmlhint/HTMLHint
 - validator/validator
 - CSSLint/csslint
 - PyCQA/pycodestyle
 - PyCQA/flake8
 - psf/black
 - checkstyle/checkstyle
 - rubocop/rubocop
 - oclint/oclint
 - dominikh/go-tools
 - ndmitchell/hlint
 - coala/coala
 - pre-commit/pre-commit
 - innogames/igcommit
 - rodjek/puppet-lint
 - koalaman/shellcheck
 - r-lib/lintr
 - testdouble/standard
 - realm/SwiftLint
 - replicatedhq/dockerfilelint
 - mvdan/sh
 - ansible/ansible-lint
 - dotenv-linter/dotenv-linter
display_name: Clean code linters
created_by: holman
---
Make sure your code matches your style guide with these essential code linters.
 17  
collections/code-quality-in-php/index.md
@@ -0,0 +1,17 @@
---
items:
 - squizlabs/PHP_CodeSniffer
 - FriendsOfPHP/PHP-CS-Fixer
 - psecio/parse
 - phan/phan
 - sebastianbergmann/phpcpd
 - sebastianbergmann/phploc
 - povils/phpmnd
 - phpmd/phpmd
 - phpstan/phpstan
 - vimeo/psalm
 - infection/infection
display_name: Code Quality Checker Tools For PHP Projects
created_by: umutphp
---
A collection of code quality tools for PHP projects that you can use to analyze and fix your code in your local environments or in CI pipelines. The collection will not contain testing tools.
 14  
collections/css-frameworks/index.md
@@ -0,0 +1,14 @@
---
items:
 - twbs/bootstrap
 - foundation/foundation-sites
 - jgthms/bulma
 - uikit/uikit
 - Semantic-Org/Semantic-UI
 - Dogfalo/materialize
 - pure-css/pure
 - tailwindlabs/tailwindcss
display_name: CSS Frameworks
created_by: krishdevdb
---
A CSS framework is a set of css classes that allow you to create your website with little to no new css code.
 BIN +144 KB 
collections/ctf-cybersec-resources/ctf-cybersec-resources.png
Unable to render rich display

 71  
collections/ctf-cybersec-resources/index.md
@@ -0,0 +1,71 @@
---
items:
 - swisskyrepo/PayloadsAllTheThings
 - vitalysim/Awesome-Hacking-Resources
 - danielmiessler/SecLists
 - bettercap/bettercap
 - samratashok/nishang
 - blaCCkHatHacEEkr/PENTESTING-BIBLE
 - Gallopsled/pwntools
 - zardus/ctf-tools
 - tegal1337/0l4bs
 - drduh/macOS-Security-and-Privacy-Guide
 - apsdehal/awesome-ctf
 - quasar/Quasar
 - AlisamTechnology/ATSCAN
 - Ciphey/Ciphey
 - juice-shop/juice-shop
 - pwndbg/pwndbg
 - yeyintminthuhtut/Awesome-Red-Teaming
 - google/google-ctf
 - laramies/theHarvester
 - orangetw/My-CTF-Web-Challenges
 - RsaCtfTool/RsaCtfTool
 - Ignitetechnologies/Privilege-Escalation
 - w181496/Web-CTF-Cheatsheet
 - terjanq/Tiny-XSS-Payloads
 - TH3xACE/SUDO_KILLER
 - m0nad/awesome-privilege-escalation
 - Ignitetechnologies/HackTheBox-CTF-Writeups
 - l4wio/CTF-challenges-by-me
 - Shiva108/CTF-notes
 - GTFOBins/GTFOBins.github.io
 - Ignitetechnologies/Vulnhub-CTF-Writeups
 - trailofbits/ctf-challenges
 - teambi0s/InCTFi
 - netlight/security-challenge
 - gophish/gophish
 - veeral-patel/how-to-secure-anything
 - teambi0s/InCTF
 - LOLBAS-Project/LOLBAS
 - devploit/CTF_OnlineTools
 - teambi0s/BSides-CTF
 - cliffe/SecGen
 - vaib25vicky/awesome-mobile-security
 - mantvydasb/RedTeaming-Tactics-and-Techniques
 - api0cradle/UltimateAppLockerByPassList
 - hisxo/gitGraber
 - S3cur3Th1sSh1t/WinPwn
 - sherlock-project/sherlock
 - s0md3v/Photon
 - jivoi/awesome-osint
 - Manisso/fsociety
 - j3ssie/osmedeus
 - rsmusllp/king-phisher
 - abhisharma404/vault
 - t0thkr1s/revshellgen
 - tina1998612/Awesome-Security-Tool-List
 - pentestmonkey/pysecdump
 - belane/I-CTF-FWHIBBIT
 - ihebski/factordb
 - CFI-UL/2018-CFI-CTF
 - Execut3/Held-CTF
 - teambi0s/InCTFj
 - trimstray/the-book-of-secret-knowledge
 - ctf-wiki/ctf-wiki
 - eciavatta/caronte
display_name: Capture The Flag (CTF) and Cyber Security Resources
created_by: Mr-Skully
image: ctf-cybersec-resources.png
---
A collections of tools, scripts, write-ups, and other essentials on GitHub that can help you improve your Cyber Security skills and ace your next CTF challenge.
 38  
collections/demo-sources/index.md
@@ -0,0 +1,38 @@
---
items:
 - farbrausch/fr_public
 - ninjadev/re
 - ninjadev/revision-invite-2018
 - ninjadev/pluss
 - ninjadev/si
 - ninjadev/dvoje
 - demoscene-source-archive/okiedokie
 - demoscene-source-archive/oscar-s-chair
 - demoscene-source-archive/blue-morpho
 - demoscene-source-archive/from-the-seas-to-the-stars
 - demoscene-source-archive/alive-here-now-forever
 - demoscene-source-archive/dropletia
 - demoscene-source-archive/glittermorphosis
 - demoscene-source-archive/horizon-machine
 - demoscene-source-archive/iiii-iv
 - demoscene-source-archive/love-reaction
 - demoscene-source-archive/ohanami
 - demoscene-source-archive/terrarium
 - armak/Hydrokinetics
 - logicomacorp/makeshift
 - excess-demogroup/amoeba
 - theblacklotus/suicide-barbie
 - theblacklotus/4edges
 - demoscene-source-archive/world-domination
 - jarnoh/doomsday
 - Moon70/ParallelMultiverse
 - aras-p/BlackHoleDemo
 - CookieCollective/Evoke-2019-4k
 - w23/jetlag_appear
 - in4k/crawlspace
 - monadgroup/sy17
 - monadgroup/re19

display_name: Demo sources
---
Take a peek behind the curtain of the finest demoscene work!
 21  
collections/design-essentials/index.md
@@ -0,0 +1,21 @@
---
items:
 - twbs/bootstrap
 - animate-css/animate.css
 - nathansmith/960-Grid-System
 - necolas/normalize.css
 - ionic-team/ionicons
 - designmodo/Flat-UI
 - h5bp/html5-boilerplate
 - foundation/foundation-sites
 - Modernizr/Modernizr
 - twbs/ratchet
 - IanLunn/Hover
 - connors/photon
 - basscss/basscss
 - atlemo/SubtlePatterns
 - mrmrs/colors
display_name: Design essentials
created_by: jonrohan
---
This collection of design libraries are the best on the web, and will complete your toolset for designing stunning products.
 42  
collections/devops-tools/index.md
@@ -0,0 +1,42 @@
---
items:
 - puppetlabs/puppet
 - chef/chef
 - ansible/ansible
 - hashicorp/terraform
 - saltstack/salt
 - apache/maven
 - gradle/gradle
 - hashicorp/vagrant
 - openstack/openstack
 - moby/moby
 - capistrano/capistrano
 - statsd/statsd
 - graphite-project/graphite-web
 - elastic/logstash
 - prometheus/prometheus
 - fabric/fabric
 - grafana/grafana
 - StackStorm/st2
 - openshift/origin
 - getsentry/sentry
 - deployphp/deployer
 - kubernetes/kubernetes
 - netdata/netdata
 - cloud66-oss/habitus
 - hashicorp/consul
 - hashicorp/nomad
 - spinnaker/spinnaker
 - Kong/kong
 - jenkinsci/jenkins
 - apache/mesos
 - SeleniumHQ/selenium
 - opendiffy/diffy
 - harness/drone
 - hashicorp/vault
 - NagiosEnterprises/nagioscore
 - zabbix/zabbix

display_name: DevOps tools
---
These tools help you manage servers and deploy happier and more often with more confidence.
 BIN +21.9 KB 
collections/fantasy-consoles/fantasy-consoles.png
Unable to render rich display

 16  
collections/fantasy-consoles/index.md
@@ -0,0 +1,16 @@
---
items:
 - PixelVision8/PixelVision8
 - https://www.youtube.com/watch?v=UKVNmHCxwk4
 - LIKO-12/LIKO-12
 - nesbox/TIC-80
 - pico-8/awesome-PICO-8
 - paladin-t/b8
 - kitao/pyxel
 - le-doux/bitsy
 - morgan3d/quadplay
 - emmachase/Riko4
display_name: Fantasy Consoles
created_by: leereilly
---
Fantasy consoles are mini game engines or virtual machines that simulate 8-bit computers and consoles from yesteryear. They force developers to work within constraints on color palettes, sound channels, resolution, memory, etc. Very popular in the retrogaming and game jam scenes. Try one out - they're super fun!
 31  
collections/front-end-javascript-frameworks/index.md
@@ -0,0 +1,31 @@
---
items:
 - solidjs/solid
 - marko-js/marko
 - MithrilJS/mithril.js
 - angular/angular
 - emberjs/ember.js
 - knockout/knockout
 - tastejs/todomvc
 - spine/spine
 - vuejs/vue
 - Polymer/polymer
 - facebook/react
 - finom/seemple
 - aurelia/framework
 - optimizely/nuclear-js
 - jashkenas/backbone
 - dojo/dojo
 - jorgebucaran/hyperapp
 - riot/riot
 - Daemonite/material
 - lit/lit
 - aurelia/aurelia
 - sveltejs/svelte
 - neomjs/neo
 - preactjs/preact
 - ionic-team/stencil
display_name: Front-end JavaScript frameworks
created_by: jonrohan
---
While the number of ways to organize JavaScript is almost infinite, here are some tools that help you build single-page applications.
 59  
collections/game-engines/index.md
@@ -0,0 +1,59 @@
---
items:
 - godotengine/godot
 - turbulenz/turbulenz_engine
 - TorqueGameEngines/Torque3D
 - TorqueGameEngines/Torque2D
 - spring/spring
 - cocos2d/cocos2d-x
 - Gamua/Starling-Framework
 - gameplay3d/gameplay
 - jMonkeyEngine/jmonkeyengine
 - SFTtech/openage
 - MonoGame/MonoGame
 - libgdx/libgdx
 - superpowers/superpowers-core
 - AtomicGameEngine/AtomicGameEngine
 - 4ian/GDevelop
 - urho3d/Urho3D
 - methusalah/OpenRTS
 - photonstorm/phaser
 - melonjs/melonJS
 - BabylonJS/Babylon.js
 - WhitestormJS/whs.js
 - wellcaffeinated/PhysicsJS
 - playcanvas/engine
 - cocos2d/cocos2d-html5
 - craftyjs/Crafty
 - pixijs/pixijs
 - renpy/renpy
 - OpenRA/OpenRA
 - OpenRCT2/OpenRCT2
 - stride3d/stride
 - lance-gg/lance
 - panda3d/panda3d
 - OpenXRay/xray-16
 - korlibs/korge
 - raysan5/raylib
 - amethyst/amethyst
 - orx/orx
 - nCine/nCine
 - cocos/cocos-engine
 - love2d/love
 - coronalabs/corona
 - hajimehoshi/ebiten
 - HaxeFoundation/haxe
 - bevyengine/bevy
 - Esenthel/EsenthelEngine
 - aws/lumberyard
 - FlaxEngine/FlaxEngine
 - ObEngine/ObEngine
 - KilledByAPixel/LittleJS
 - ppy/osu-framework
 - gosu/gosu
 - cerberusxdev/cerberus
 - ganelson/inform
display_name: Game Engines
created_by: leereilly
---
Frameworks for building games across multiple platforms.
 45  
collections/github-browser-extensions/index.md
@@ -0,0 +1,45 @@
---
items:
 - ovity/octotree
 - mike-north/chrome-github-boxcutter
 - muan/github-dashboard
 - muan/github-gmail
 - thieman/github-selfies
 - Yatser/prettypullrequests
 - sanemat/do-not-merge-wip-for-github
 - jasonlong/isometric-contributions
 - ForbesLindesay/github-real-names
 - benbalter/github-mention-highlighter
 - sindresorhus/notifier-for-github
 - OctoLinker/OctoLinker
 - ProLoser/Github-Omnibox
 - Justineo/github-hovercard
 - panzerdp/clipboardy
 - zenorocha/codecopy
 - kamranahmedse/githunt
 - harshjv/github-repo-size
 - refined-github/refined-github
 - softvar/enhanced-github
 - bitoiu/markwrap
 - bitoiu/github-red-alert
 - Kibibit/achievibit
 - marpo60/github-compare-tags
 - cheshire137/hubnav
 - ryanflorence/github-plusone-extension
 - Mottie/GitHub-userscripts
 - rgehan/octolenses
 - homerchen19/github-file-icons
 - StylishThemes/GitHub-Dark
 - xthexder/wide-github
 - berzniz/github_pr_tree
 - N1ck/gifs-for-github
 - EnixCoda/Gitako
 - vladgolubev/quickreview-for-github
 - matthizou/github-show-avatars
 - dderevjanik/github-vscode-icons
 - npmhub/npmhub
 - octobox/extension
display_name: GitHub Browser Extensions
created_by: leereilly
---
Some useful and fun browser extensions to personalize your GitHub browser experience.
 BIN +121 KB 
collections/github-pages-examples/github-pages-examples.png
Unable to render rich display

 19  
collections/github-pages-examples/index.md
@@ -0,0 +1,19 @@
---
items:
 - twbs/bootstrap
 - jekyll/jekyll
 - github/government.github.com
 - electron/electronjs.org
 - square/square.github.io
 - twitter/twitter.github.io
 - Netflix/netflix.github.com
 - Yelp/yelp.github.io
 - facebook/react
 - artsy/artsy.github.io
 - Metroxe/one-html-page-challenge
 - fairfield-programming/fairfield-programming.github.io
display_name: GitHub Pages examples
created_by: jdennes
image: github-pages-examples.png
---
Fine examples of projects using GitHub Pages (https://pages.github.com).
 BIN +60.4 KB 
collections/government/government.png
Unable to render rich display

 31  
collections/government/index.md
@@ -0,0 +1,31 @@
---
items:
 - 18F/development-guide
 - cfpb/open-source-checklist
 - alphagov/whitehall
 - nasa/openmct
 - codeforamerica/adopt-a-hydrant
 - 18F/ads-bpa
 - project-open-data/project-open-data.github.io
 - opengovfoundation/madison
 - GSA/data.gov
 - ngageoint/geoq
 - wet-boew/wet-boew
 - CityOfPhiladelphia/flu-shot-spec
 - nysenate/OpenLegislation
 - openlexington/gethelplex
 - uscensusbureau/citysdk
 - NREL/api-umbrella
 - usds/playbook
 - republique-et-canton-de-geneve/chvote-1-0
 - https://www.youtube.com/embed/uNa9GOtM6NE
 - gchq/CyberChef
 - HSEIreland/covid-tracker-app
 - nic-delhi/AarogyaSetu_Android
 - govCMS/GovCMS
 - coloradodigitalservice/exposure-notifications-metrics-public
display_name: Government apps
created_by: jbjonesjr
image: government.png
---
Sites, apps, and tools built by governments across the world to make government work better, together.  Read more at [government.github.com](https://government.github.com).
 BIN +9.46 KB 
collections/hacking-minecraft/hacking-minecraft.png
Unable to render rich display

 34  
collections/hacking-minecraft/index.md
@@ -0,0 +1,34 @@
---
items:
 - docker/dockercraft
 - minefold/hubot-minecraft
 - leereilly/hubot-minecraft-skin
 - overviewer/Minecraft-Overviewer
 - cuberite/cuberite
 - pmmp/PocketMine-MP
 - msmhq/msm
 - essentials/Essentials
 - VazkiiMods/Botania
 - MightyPirates/OpenComputers
 - PrismarineJS/mineflayer
 - EngineHub/WorldEdit
 - SpigotMC/BungeeCord
 - walterhiggins/ScriptCraft
 - MinecraftForge/MinecraftForge
 - ddevault/TrueCraft
 - MachineMuse/MachineMusePowersuits
 - micdoodle8/Galacticraft
 - Bukkit/Bukkit
 - GlowstoneMC/Glowstone
 - MovingBlocks/Terasology
 - Zerite/CraftLib
 - PaperMC/Paper
 - CaffeineMC/sodium-fabric
 - FabricMC/fabric
 - lambda-client/lambda/
 - nerdsinspace/nocom-explanation
display_name: Hacking Minecraft
created_by: leereilly
image: hacking-minecraft.png
---
Minecraft is a game about building blocks, but it doesn’t end there. Take Minecraft further with some of the projects below, or dive into the code mines and hammer your own!
 12  
collections/internet-censorship-circumventions/index.md
@@ -0,0 +1,12 @@
---
items:
 - Dreamacro/clash
 - Psiphon-Inc/psiphon
 - getlantern/lantern
 - shadowsocks/shadowsocks
 - trojan-gfw/trojan
 - v2fly/v2ray-core
display_name: Internet Censorship Circumventions
created_by: new-pac
---
Internet censorship circumvention is the use of various methods and tools to bypass internet censorship.
 29  
collections/javascript-game-engines/index.md
@@ -0,0 +1,29 @@
---
items:
 - pixijs/pixijs
 - photonstorm/phaser
 - melonjs/melonJS
 - gamelab/kiwi.js
 - craftyjs/Crafty
 - liabru/matter-js
 - shakiba/stage.js
 - cocos2d/cocos2d-html5
 - playcanvas/engine
 - cookiengineer/lycheejs
 - BabylonJS/Babylon.js
 - ekelokorpi/panda-engine
 - qiciengine/qiciengine
 - WhitestormJS/whs.js
 - GooTechnologies/goojs
 - shakiba/planck.js
 - Irrelon/ige
 - 4ian/GDevelop
 - mrdoob/three.js
 - phoboslab/Impact
 - Cloud9c/taro
 - replit/kaboom
 - straker/kontra
display_name: JavaScript Game Engines
created_by: leereilly
---
Learn or level up your 1337 gamedev skills and build amazing games together for web, desktop, or mobile using these HTML5 / JavaScript game engines.
 15  
collections/javascript-state-management/index.md
@@ -0,0 +1,15 @@
---
items:
 - effector/effector
 - mobxjs/mobx
 - reduxjs/redux
 - Yomguithereal/baobab
 - immerjs/immer
 - statelyai/xstate
 - cerebral/cerebral
 - storeon/storeon
 - artalar/reatom
display_name: JavaScript State Management Tools
created_by: lestad
---
Framework agnostic libraries to manage state in JavaScript applications.
 40  
collections/learn-to-code/index.md
@@ -0,0 +1,40 @@
---
items:
 - 30-seconds/30-seconds-of-code
 - railsgirls/railsgirls.github.io
 - railsbridge/docs
 - freeCodeCamp/freeCodeCamp
 - leachim6/hello-world
 - datasciencemasters/go
 - practical-tutorials/project-based-learning
 - zhiwehu/Python-programming-exercises
 - MunGell/awesome-for-beginners
 - appacademy/welcome-to-open
 - lostdesign/webgems
 - kamranahmedse/developer-roadmap
 - AMAI-GmbH/AI-Expert-Roadmap
 - trekhleb/javascript-algorithms
 - microsoft/Web-Dev-For-Beginners
 - karan/Projects
 - sindresorhus/awesome
 - donnemartin/system-design-primer
 - codecrafters-io/build-your-own-x
 - public-apis/public-apis
 - EbookFoundation/free-programming-books
 - amitness/learning
 - mhinz/vim-galore
 - tayllan/awesome-algorithms
 - karlhorky/learn-to-program
 - therebelrobot/awesome-workshopper
 - jlevy/the-art-of-command-line
 - papers-we-love/papers-we-love
 - awesome-selfhosted/awesome-selfhosted
 - ripienaar/free-for-dev
 - gothinkster/realworld
 - thedaviddias/Front-End-Checklist

display_name: Learn to Code
created_by: alysonla
image: learn-to-code.png
---
Resources to help people learn to code
 BIN +8.45 KB 
collections/learn-to-code/learn-to-code.png
Unable to render rich display

 39  
collections/load-testing/index.md
@@ -0,0 +1,39 @@
---
items:
 - americanexpress/baton
 - artilleryio/artillery
 - apigee/apib
 - apache/jmeter
 - bengadbois/pewpew
 - Blazemeter/taurus
 - BuoyantIO/slow_cooker
 - codesenberg/bombardier
 - fcsonline/drill
 - flood-io/element
 - fortio/fortio
 - gatling/gatling
 - goadapp/goad
 - GoogleChrome/lighthouse
 - hatoo/oha
 - lighttpd/weighttp
 - grafana/k6
 - loads/molotov
 - locustio/locust
 - mcollina/autocannon
 - mhausenblas/kboom
 - pinterest/bender
 - rabbitmq/rabbitmq-perf-test
 - rakyll/hey
 - RedisLabs/memtier_benchmark
 - rogerwelin/cassowary
 - satori-com/tcpkali
 - tsenart/vegeta
 - processone/tsung
 - wg/wrk
 - yandex/yandex-tank
 - Zooz/predator
display_name: Load testing
created_by: jucke
image: load-testing.png
---
Load testing, benchmarking and stress testing tools.
 BIN +22.8 KB 
collections/load-testing/load-testing.png
Unable to render rich display

 45  
collections/machine-learning/index.md
@@ -0,0 +1,45 @@
---
items:
 - apache/spark
 - apache/hadoop
 - jbhuang0604/awesome-computer-vision
 - GSA/data
 - GoogleTrends/data
 - nationalparkservice/data
 - fivethirtyeight/data
 - beamandrew/medical-data
 - src-d/awesome-machine-learning-on-source-code
 - igrigorik/decisiontree
 - keon/awesome-nlp
 - openai/gym
 - aikorea/awesome-rl
 - umutisik/Eigentechno
 - jpmckinney/tf-idf-similarity
 - scikit-learn-contrib/lightning
 - gwding/draw_convnet
 - scikit-learn/scikit-learn
 - tensorflow/tensorflow
 - activeloopai/Hub
 - Theano/Theano
 - shogun-toolbox/shogun
 - davisking/dlib
 - apache/predictionio
 - deepmind/pysc2
 - gokceneraslan/awesome-deepbio
 - buriburisuri/ByteNet
 - josephmisiti/awesome-machine-learning
 - ujjwalkarn/Machine-Learning-Tutorials
 - ChristosChristofidis/awesome-deep-learning
 - fastai/courses
 - Yorko/mlcourse.ai
 - jtoy/awesome-tensorflow
 - nlintz/TensorFlow-Tutorials
 - pkmital/tensorflow_tutorials
 - https://www.youtube.com/embed/KsbQ_HNX6Pg
 - https://www.youtube.com/embed/bHvf7Tagt18
 - https://www.youtube.com/embed/ILsA4nyG7I0
 - AMAI-GmbH/AI-Expert-Roadmap
display_name: Getting started with machine learning
created_by: omoju
---
Today, machine learning—the study of algorithms that make data-based predictions—has found a new audience and a new set of possibilities.
 25  
collections/made-in-africa/index.md
@@ -0,0 +1,25 @@
---
items:
 - acekyd/made-in-nigeria
 - frontlinesms/frontlinesms2
 - ushahidi/SMSSync
 - praekeltfoundation/vumi
 - rapidpro/rapidpro
 - pluspeople/pesaPi
 - praekeltfoundation/junebug
 - chisimba/chisimba
 - OpenInstitute/OpenDuka
 - CodeForAfrica/GotToVote
 - universalcore/elastic-git
 - nyaruka/smartmin
 - gernest/utron
 - ushahidi/platform
 - Yorubaname/yorubaname-website
 - codedivoire/made-in-ci
 - beopencloud/cno
 - javascriptdata/danfojs
 - bangajs/banga-cli
display_name: Made in Africa
created_by: mozzadrella
---
Developers in Africa use open source technology to solve some of the world's most intractable problems and grow their business ecosystems. Here's a snapshot of local projects across the continent.
 20  
collections/made-in-argentina/index.md
@@ -0,0 +1,20 @@
---
items:
  - code-hike/codehike
  - mgonto/restangular
  - play-with-docker/play-with-docker
  - redis/jedis
  - Mango/slideout
  - pazguille/offline-first
  - Aerolab/midnight.js
  - cazala/synaptic
  - cazala/coin-hive
  - decentraland/marketplace
  - dropwizard/dropwizard
  - andresriancho/w3af
display_name: Made in Argentina
created_by: marcosnils
image: made-in-argentina.png
---

Open source projects built in or receiving significant contributions from Argentina :argentina:
 BIN +32.6 KB 
collections/made-in-argentina/made-in-argentina.png
Unable to render rich display

 36  
collections/made-in-bangladesh/index.md
@@ -0,0 +1,36 @@
---
items:
 - adar2378/pin_code_fields
 - Agontuk/react-native-geolocation-service
 - alamkanak/Android-Week-View
 - appzcoder/crud-generator
 - appzcoder/laravel-admin
 - halfo/lambda-mod-zsh-theme
 - LordAmit/Brightness
 - mmahmoodictbd/production-ready-microservices-starter
 - mugli/Avro-Keyboard
 - nahid/gohttp
 - nahid/jsonq
 - nahid/talk
 - neurobin/shc
 - nuhil/bangladesh-geocode
 - OpenBangla/OpenBangla-Keyboard
 - proshoumma/react-native-off-canvas-menu
 - s1s1ty/py-jsonq
 - safwanrahman/django-webpush
 - sagorbrur/bnlp
 - sarim/ibus-avro
 - Shafin098/pakhi-bhasha
 - tareq1988/wordpress-settings-api-class
 - tareq1988/wp-eloquent
 - thedevsaddam/gojsonq
 - thedevsaddam/govalidator
 - thesabbir/simple-line-icons
 - usmanhalalit/charisma
 - usmanhalalit/laracsv

display_name: Made in Bangladesh
created_by: kuttumiah
image: made-in-bangladesh.png
---
Open source projects built in or receiving significant contributions from Bangladesh :bangladesh:
 BIN +10.3 KB 
collections/made-in-bangladesh/made-in-bangladesh.png
Unable to render rich display

 26  
collections/made-in-brazil/index.md
@@ -0,0 +1,26 @@
---
items:
  - clappr/clappr
  - devhubapp/devhub
  - dracula/dracula-theme
  - elixir-lang/elixir
  - ellisonleao/magictools
  - felipefialho/frontend-challenges
  - go-task/task
  - heartcombo/devise
  - htop-dev/htop/
  - lua/lua
  - RocketChat/Rocket.Chat
  - tsuru/tsuru
  - goreleaser/goreleaser
  - Universidade-Livre/ciencia-da-computacao
  - cuducos/minha-receita
  - kvnol/aprenda-frontend
  - felipeorlando/aprenda-rubyonrails
  - BrasilAPI/BrasilAPI
  - alt-art/commit
display_name: Made in Brazil
created_by: caarlos0
image: made-in-brazil.png
---
Open source projects built in or receiving significant contributions from Brazil :brazil:
 BIN +19.5 KB 
collections/made-in-brazil/made-in-brazil.png
Unable to render rich display

 47  
collections/made-in-china/index.md
@@ -0,0 +1,47 @@
---
items:
 - alibaba/arthas
 - alibaba/p3c
 - alibaba/druid
 - alibaba/fastjson
 - alibaba/flutter-go
 - Tencent/weui
 - Tencent/wepy
 - Tencent/tinker
 - Tencent/mars
 - Tencent/weui-wxss
 - Tencent/vConsole
 - Tencent/QMUI_Android
 - Tencent/MMKV
 - Tencent/omi
 - Tencent/ncnn
 - Tencent/VasSonic
 - Tencent/rapidjson
 - Tencent/APIJSON
 - baidu/amis
 - baidu/san
 - baidu/uid-generator
 - CHINA-JD/presto
 - ElemeFE/element
 - ElemeFE/mint-ui
 - ElemeFE/node-interview
 - ElemeFE/v-charts
 - apolloconfig/apollo
 - NetEase/pomelo
 - Meituan-Dianping/mpvue
 - Meituan-Dianping/walle
 - dianping/cat
 - XiaoMi/soar
 - XiaoMi/mace
 - didi/DoKit
 - didi/cube-ui
 - didi/chameleon
 - didi/VirtualAPK
 - bilibili/ijkplayer
 - bilibili/flv.js
 - bilibili/DanmakuFlameMaster
display_name: Made in China
created_by: renfei
image: made-in-china.png
---
Open source projects built in or receiving significant contributions from China :cn:
 BIN +4.7 KB 
collections/made-in-china/made-in-china.png
Unable to render rich display

 28  
collections/made-in-cuba/index.md
@@ -0,0 +1,28 @@
---
items:
 - n3omaster/bachecubano
 - codestrange
 - codestrange/matcom-messenger
 - daxslab/fotorecarga
 - aleguerra05/metro_trans
 - garciaguimeras/PorLaLivreApp
 - Develop-Genesis/Graphql-Controller
 - jadolg/vpn2go
 - jadolg/rocketchat_API
 - pavelmc/FT857d
 - pavelmc/Si5351mcu
 - pavelmc/Yatuli
 - pavelmc/BMux
 - pavelmc/arduino-arcs
 - pavelmc/carrito
 - pavelmc/carrito-control
 - pavelmc/multi-probe-swr-meter
 - stdevPavelmc/esp8266_wx_station
 - Pixely-Studios/NStart

display_name: Made in Cuba
created_by: lopezdp
image: made-in-cuba.png
---

Open source projects built in or receiving significant contributions from Cuba :cuba:
 BIN +122 KB 
collections/made-in-cuba/made-in-cuba.png
Unable to render rich display

 9  
collections/made-in-denmark/index.md
@@ -0,0 +1,9 @@
---
items:
 - toitlang/toit

display_name: Made in Denmark
created_by: snxx-lppxx
image: made-in-denmark.png
---
Open source projects built in or receiving significant contributions from Denmark :denmark:
 BIN +2.04 KB 
collections/made-in-denmark/made-in-denmark.png
Unable to render rich display

 28  
collections/made-in-egypt/index.md
@@ -0,0 +1,28 @@
---
items:
  - KL13NT/ally-reads
  - RobustaStudio/bkit
  - aliftype/amiri
  - Gue3bara/Cairo
  - logaretm/vee-validate
  - ahmadalfy/workflow
  - themsaid/wink
  - RobustaStudio/Resala
  - swvl/express-versioned-route
  - Ahmed-Ali/JSONExport
  - piscibus/notifly
  - gogearbox/gearbox
  - ahegazy/php-mvc-skeleton
  - aboul3la/Sublist3r
  - ShaftHQ/SHAFT_ENGINE
  - fawry-api/fawry
  - harryadel/AI-ML-Driven-Companies-In-Egypt
  - abdumostafa/awesome-in-arabic
  - hci-lab/PyQuran
  - DrWaleedAYousef/Teaching
  - MoatazBadawy/Sunset-hadith
display_name: Made in Egypt
created_by: AN4553R
image: made-in-egypt.png
---
Open source projects built in or receiving significant contributions from Egypt 🇪🇬
 BIN +101 KB 
collections/made-in-egypt/made-in-egypt.png
Unable to render rich display

 24  
collections/made-in-france/index.md
@@ -0,0 +1,24 @@
---
items:
 - strapi/strapi
 - Qovery/engine
 - symfony/symfony
 - scikit-learn/scikit-learn
 - marmelab/react-admin
 - fzaninotto/Faker
 - huggingface
 - traefik
 - deezer/spleeter
 - algolia/places
 - ovh/cds
 - nuxt
 - api-platform/api-platform
 - lichess-org/lila
 - GitbookIO/gitbook
 - mui/material-ui
 - PrestaShop/PrestaShop
display_name: Made in France
created_by: ferdi05
image: made-in-france.png
---
Open source projects built in or receiving significant contributions from France :fr:
 BIN +856 Bytes 
collections/made-in-france/made-in-france.png
Unable to render rich display

 54  
collections/made-in-india/index.md
@@ -0,0 +1,54 @@
---
items:
 - TheAlgorithms
 - odpf
 - hoppscotch/hoppscotch
 - anuraghazra/github-readme-stats
 - hasura/graphql-engine
 - GeekyAnts/NativeBase
 - kovidgoyal/calibre
 - frappe/erpnext
 - chatwoot/chatwoot
 - openebs/openebs  
 - fission/fission
 - covid19india/covid19india-react
 - knadh/listmonk
 - appsmithorg/appsmith
 - bagisto/bagisto
 - gluster/glusterfs
 - frappe/frappe
 - spacecloud-io/space-cloud
 - shivammathur/setup-php
 - kubeshop/botkube
 - SigNoz/signoz
 - uvdesk/community-skeleton
 - CRED-CLUB/synth-android
 - resetercss/reseter.css
 - pupilfirst/pupilfirst
 - kadalu/kadalu
 - project-sunbird
 - composewell/streamly
 - SecurityFTW/cs-suite
 - adithyakhamithkar/ansible-playbooks
 - ToolJet/ToolJet
 - ParthJadhav/Tkinter-Designer
 - dr5hn/countries-states-cities-database
 - glific/glific
 - gautamkrishnar/blog-post-workflow
 - devtron-labs/devtron
 - skytable/skytable
 - firstcontributions/first-contributions
 - CircuitVerse/CircuitVerse
 - mayankmetha/Rucky
 - resuminator
 - krayin/laravel-crm
 - ajeetdsouza/zoxide
 - Jaysmito101/TerraForge3D
 - keploy/keploy
 - LambdaTest/test-at-scale
 - arnav-kr/json-formatter
display_name: Made in India
created_by: mvkaran
image: made-in-india.png
---
Open source projects built in or receiving significant contributions from India :india:
 BIN +8.79 KB 
collections/made-in-india/made-in-india.png
Unable to render rich display

 16  
collections/made-in-indonesia/index.md
@@ -0,0 +1,16 @@
---
items:
 - ariya/phantomjs
 - hyperjumptech/grule-rule-engine
 - kawalcovid19
 - usetania/tania-core
 - OpenSID
 - BaritoLog
 - odpf
 - xitorch/xitorch
 - mathdroid/covid-19-api
display_name: Made in Indonesia
created_by: mabdh
image: made-in-indonesia.png
---
Open source projects built in or receiving significant contributions from Indonesia :indonesia:
 BIN +3.35 KB 
collections/made-in-indonesia/made-in-indonesia.png

 23  
collections/made-in-iran/index.md
@@ -0,0 +1,23 @@
---
items:
 - jadijadi/bestoon
 - jadijadi/justforfun
 - mr-hashemi/mr-hashemi
 - pashmaklang/pashmak
 - sobhe/moratab
 - PyFarsi/pyabr
 - jamedadi/yummy
 - sobhe/hazm
 - rastikerdar/vazirmatn
 - mohebifar/made-in-iran
 - Hameds/APIs-made-in-Iran
 - Kiarash-Z/react-modern-calendar-datepicker
 - MahdiMajidzadeh/bootstrap-v4-rtl
 - imaNNeoFighT/fl_chart
 - persian-tools/persian-tools
 - usablica/intro.js
display_name: Made in Iran
created_by: Javad
image: made-in-iran.png
---
Iranian developer's list of open source projects :iran:
 BIN +7.65 KB 
collections/made-in-iran/made-in-iran.png

 19  
collections/made-in-israel/index.md
@@ -0,0 +1,19 @@
---
items:
 - yaronn/blessed-contrib
 - getredash/redash
 - cool-RR/PySnooper
 - wix/react-native-navigation
 - linnovate/mean
 - appwrite/appwrite
 - snyk/cli
 - ealush/vest
 - aantn/smag
 - lirantal/licenseye
 - lirantal/dockly
 - eranroz/HspellPy
display_name: Made in Israel
created_by: donno2048
image: made-in-israel.png
---
Open source projects built in Israel :israel:
 BIN +5.35 KB 
collections/made-in-israel/made-in-israel.png

 24  
collections/made-in-italy/index.md
@@ -0,0 +1,24 @@
---
items:
 - fastify/fastify
 - immuni-app/immuni
 - italia/covid19-opendata-vaccini
 - middyjs/middy
 - nodejs/undici
 - elastic/elasticsearch-js
 - morrolinux/simple-ehm
 - micheleriva/coronablocker
 - HospitalRun/hospitalrun
 - histolab/histolab
 - strawberry-graphql/strawberry
 - notable/notable
 - espanso/espanso
 - eciavatta/caronte
 - Schrodinger-Hat/ImageGoNord
 - stoplightio/prism
 - ercole-io/ercole
display_name: Made in Italy
created_by: thejoin95
image: made-in-italy.png
---
Open source projects built in or receiving significant contributions from Italy :it:
 BIN +9.23 KB 
collections/made-in-italy/made-in-italy.png

 9  
collections/made-in-kazakhstan/index.md
@@ -0,0 +1,9 @@
---
items:
 - scdesktop/scdesktop

display_name: Made in Kazakhstan
created_by: snxx-lppxx
image: made-in-kazakhstan.png
---
Open source projects built in or receiving significant contributions from Kazakhstan :kazakhstan:
 BIN +11 KB 
collections/made-in-kazakhstan/made-in-kazakhstan.png

 25  
collections/made-in-korea/index.md
@@ -0,0 +1,25 @@
---
items:
  - junegunn/fzf
  - junegunn/vim-plug
  - yunjey/pytorch-tutorial
  - nhn/tui.editor
  - posquit0/Awesome-CV
  - pinpoint-apm/pinpoint
  - summernote/summernote
  - kjw0612/awesome-deep-vision
  - line/armeria
  - gyoogle/tech-interview-for-developer
  - milooy/remote-or-flexible-work-company-in-korea
  - 738/awesome-sushi
  - konlpy/konlpy
  - javascript-tutorial/ko.javascript.info
  - ClintJang/awesome-swift-korean-lecture
  - SKTBrain/KoBERT
  - line/centraldogma
display_name: Made in Korea
created_by: ywroh
image: made-in-korea.png
---

Open source projects built in or receiving significant contributions from Korea :kr:
 BIN +24.9 KB 
collections/made-in-korea/made-in-korea.png

 28  
collections/made-in-mauritius/index.md
@@ -0,0 +1,28 @@
---
items:
  - Humeira/made-in-Mauritius
  - Nayar/KDE-for-Mauritius
  - sjdvda/myt-usage-checker
  - mscraftsman/devcon2019
  - percymamedy/laravel-dev-booter
  - findbrok/php-watson-api-bridge
  - findbrok/laravel-personality-insights
  - LaraChimp/mango-repo
  - LaraChimp/pine-annotations
  - percymamedy/crafter
  - jcplaboratory/rashell
  - Abdur-rahmaanJ/greenBerry
  - Abdur-rahmaanJ/honeybot
  - Abdur-rahmaanJ/meteomoris
  - vue-gapi/vue-gapi
  - reallyaditya/mauritius-speedtest
  - MrSunshyne/mauritius-fuel-prices
  - MrSunshyne/mauritius-dataset-electricity
  - MrSunshyne/mauritius-sea-cable
  - MrSunshyne/covid19-mauritius
  - MrSunshyne/mauritius-power-outages
display_name: Made in Mauritius
created_by: Naoero
image: made-in-mauritius.png
---
Open source projects built in or receiving significant contributions from Mauritius :mauritius:
 BIN +1.05 KB 
collections/made-in-mauritius/made-in-mauritius.png

 26  
collections/made-in-poland/index.md
@@ -0,0 +1,26 @@
---
items:
 - saleor/saleor
 - callstack
 - software-mansion/react-native-reanimated
 - jsfiddle
 - impress/impress.js
 - sindresorhus/got
 - mirumee/ariadne
 - public-activity/public_activity
 - YetiForceCompany/YetiForceCRM
 - razorjack/quicksand
 - handsontable/handsontable
 - oskarkrawczyk/heyoffline
 - sickill/git-dude
 - damian-kolakowski/iOS-Hierarchy-Viewer
 - sickill/bitpocket
 - bernii/gauge.js
 - wuub/SublimeREPL
 - kamilkisiela/graphql-config

display_name: Made in Poland
created_by: Tymek
image: made-in-poland.png
---
Open source projects built in or receiving significant contributions from Poland :poland:
 BIN +6.58 KB 
collections/made-in-poland/made-in-poland.png

 8  
collections/made-in-singapore/index.md
@@ -0,0 +1,8 @@
---
items:
 - datascapesg/red-cross-blood-stocks
display_name: Made In Singapore
created_by: chadlimjinjie
image: made-in-singapore.png
---
Open source projects built in or receiving significant contributions from Singapore :singapore:
 BIN +6 KB 
collections/made-in-singapore/made-in-singapore.png

 19  
collections/made-in-spain/index.md
@@ -0,0 +1,19 @@
---
items:
 - voidcosmos/npkill
 - midudev/codi.link
 - microlinkhq/unavatar
 - svgdotjs/svg.js
 - microlinkhq/metascraper
 - react-toolbox/react-toolbox
 - midudev/covid-vacuna
 - franciscop/picnic
 - postcss/postcss
 - browserslist/browserslist
 - carloscuesta/gitmoji
display_name: Made in Spain
created_by: eschiclers
image: made-in-spain.png
---

Open source projects built in or receiving significant contributions from Spain :es: 
 BIN +4.75 KB 
collections/made-in-spain/made-in-spain.png

 28  
collections/made-in-turkey/index.md
@@ -0,0 +1,28 @@
---
items:
 - ShareX/ShareX
 - furkandeveloper/EasyProfiler
 - isidentical/refactor
 - fatih/vim-go
 - ssg/streetcoder
 - ahmetb/kubectx
 - eserozvataf/laroux.js
 - f/vue-wait
 - joom/hezarfen
 - jbytecode/LinRegOutliers
 - jbytecode/JMcDM
 - jbytecode/rcaller
 - obss/sahi
 - passwall/passwall-server
 - passwall/passwall-extension
 - passwall/passwall-desktop
 - passwall/passwall-web
 - pankod/refine
 - pankod/superplate
 - Huseyinnurbaki/mocktail
 - geziyor/geziyor
display_name: Made in Turkey
created_by: kiliczsh
image: made-in-turkey.png
---
Open source projects built in or receiving significant contributions from Turkey :tr:
 BIN +2.44 KB 
collections/made-in-turkey/made-in-turkey.png

 60  
collections/made-in-ukraine/index.md
@@ -0,0 +1,60 @@
---
items:
 - trekhleb/javascript-algorithms
 - mui/material-ui
 - ansible/ansible
 - Leaflet/Leaflet
 - denysdovhan/wtfjs
 - dimsemenov/PhotoSwipe
 - trailofbits/algo
 - uglide/RedisDesktopManager
 - trekhleb/homemade-machine-learning
 - vadimdemedes/ink
 - spaceship-prompt/spaceship-prompt
 - aio-libs/aiohttp
 - pnpm/pnpm
 - paulmillr/chokidar
 - trekhleb/learn-python
 - brunch/brunch
 - resilience4j/resilience4j
 - eclipse/che
 - dmytrodanylyk/circular-progress-button
 - valor-software/ngx-bootstrap
 - platformio/platformio-core
 - denysdovhan/bash-handbook
 - Codeception/Codeception
 - mapbox/pixelmatch
 - wasm3/wasm3
 - codeceptjs/CodeceptJS
 - paulmillr/es6-shim
 - dmytrodanylyk/android-process-button
 - glushchenko/fsnotes
 - Tyrrrz/DiscordChatExporter
 - mourner/suncalc
 - leits/MeetingBar
 - trekhleb/nano-neuron
 - mourner/simplify-js
 - dmytrodanylyk/shadow-layout
 - mourner/rbush
 - tailhook/vagga
 - JSMonk/hegel
 - Tyrrrz/YoutubeExplode
 - dmytrodanylyk/folding-plugin
 - mapbox/delaunator
 - mapbox/flamebearer
 - mourner/bullshit.js
 - mapbox/earcut
 - cherrypy/cherrypy
 - mapbox/geojson-vt
 - vadimdemedes/pastel
 - mapbox/supercluster
 - dmytrodanylyk/android-morphing-button
 - komarserjio/notejam
 - ttag-org/ttag
 - vmagamedov/grpclib
 - angrymouse/remote-functions

display_name: Made in Ukraine
image: made-in-ukraine.png
---
Open source projects built in or receiving significant contributions from Ukraine :ukraine:
 BIN +3.16 KB 
collections/made-in-ukraine/made-in-ukraine.png

 38  
collections/made-in-vietnam/index.md
@@ -0,0 +1,38 @@
---
items:
 - mlbvn
 - VinAIResearch
 - vietai
 - webuild-community
 - chiphuyen/machine-learning-systems-design
 - tiepvupsu/ebookMLCB
 - holistics/dbml
 - google/edward2
 - vncorenlp/VnCoreNLP
 - google/tink
 - blei-lab/edward
 - ZuzooVn/machine-learning-for-software-engineers
 - chiphuyen/ml-interviews-book
 - huytd/kanban-app
 - undertheseanlp/underthesea
 - tiepvupsu/tabml_book
 - hoanhan101/algo
 - vinbigdata-medical/vindr-lab
 - vanhuyz/CycleGAN-TensorFlow
 - TablePlus/TablePlus
 - khangich/machine-learning-interview
 - huytd/agar.io-clone
 - opencardev/crankshaft
 - binhnguyennus/awesome-scalability
 - hoanhan101/ultimate-go
 - ProxymanApp/Proxyman
 - bangoc123/learn-machine-learning-in-two-months
 - phuocng/csslayout
 - nguyenquangminh0711/ruby_jard
 - phuocng/1loc
 - BambooEngine/ibus-bamboo
display_name: Made in Vietnam
created_by: duythanhvn
image: made-in-vietnam.png
---
Open source projects built in or receiving significant contributions from Vietnam :vietnam:
 BIN +7.07 KB 
collections/made-in-vietnam/made-in-vietnam.png

 19  
collections/material-ui-atomic-design/index.md
@@ -0,0 +1,19 @@
---
items:
 - rubygarage/nextjs6-graphql-client-tutorial
 - MikeBild/serverless-aws-cdk-ecommerce
 - alexander-elgin/atomic-react-redux
 - fernandohenriques/chat-app
 - marcelorl/tastin-front
 - kumiko-haraguchi/live-jazz-tokyo
 - JoshEvan/StockManagementSystem
 - yudwig/next-redux-todo
 - atomixinteractions/materialized
 - takanassyi/react-and-rekognition
 - takanassyi/react-tutorial-ts-mui
 - mqckind/crowdmeeting
 - ERS-HCL/react-atomic-lib
display_name: Material-UI Projects Using Atomic Design
created_by: trentschnee
---
Find examples of projects utilizing Material-UI with the infamous atomic design system!
 BIN +13.8 KB 
collections/material-ui-atomic-design/material-ui-atomic-design.png

 33  
collections/music/index.md
@@ -0,0 +1,33 @@
---
items:
 - beetbox/beets
 - scottschiller/SoundManager2
 - CreateJS/SoundJS
 - musescore/MuseScore
 - tomahawk-player/tomahawk
 - cashmusic/platform
 - mopidy/mopidy
 - AudioKit/AudioKit
 - Soundnode/soundnode-app
 - gillesdemey/Cumulus
 - metabrainz/picard
 - overtone/overtone
 - sonic-pi-net/sonic-pi
 - nukeop/nuclear
 - swdotcom/swdc-vscode-musictime
 - hundredrabbits/Orca
 - 8bitbubsy/pt2-clone
 - 8bitbubsy/ft2-clone
 - mywave82/opencubicplayer
 - electronoora/komposter
 - BambooTracker/BambooTracker
 - theyamo/CheeseCutter
 - pete-gordon/hivelytracker
 - kometbomb/klystrack
 - schismtracker/schismtracker
 - chunkypixel/TIATracker
 - milkytracker/MilkyTracker
display_name: Music
created_by: jonrohan
---
Drop the code bass with these musically themed repositories.
 15  
collections/net-neutrality/index.md
@@ -0,0 +1,15 @@
---
items:
 - EFForg/action-center-platform
 - fightforthefuture/battleforthenet
 - fightforthefuture/battleforthenet-widget
 - mariechatfield/call-my-congress
 - mozilla/advocacy.mozilla.org
 - panxzz/NN-blackout
 - j2kao/fcc_nn_research
 - berkmancenter/internet_monitor
 - ahmia/ahmia-site
display_name: Net neutrality
image: net-neutrality.gif
---
Software, research, and organizations protecting the free and open internet.
 BIN +134 KB 
collections/net-neutrality/net-neutrality.gif

 14  
collections/open-data/index.md
@@ -0,0 +1,14 @@
---
items:
 - GSA/data
 - unitedstates/congress-legislators
 - Chicago/food-inspections-evaluation
 - OpenExoplanetCatalogue/open_exoplanet_catalogue
 - cernopendata/opendata.cern.ch
 - openaddresses/openaddresses
 - APIs-guru/openapi-directory
 - whosonfirst-data/whosonfirst-data
display_name: Open data
created_by: benbalter
---
Examples of using GitHub to store, publish, and collaborate on open, machine-readable datasets
 20  
collections/open-journalism/index.md
@@ -0,0 +1,20 @@
---
items:
 - fivethirtyeight/data
 - datadesk/notebooks
 - nytimes/objective-c-style-guide
 - newsapps/beeswithmachineguns
 - voxmedia/meme
 - propublica/guides
 - censusreporter/censusreporter
 - nprapps/app-template
 - TimeMagazineLabs/babynames
 - guardian/frontend
 - dukechronicle/chronline
 - BloombergMedia/whatiscode
 - times/cardkit
 - mkiser/WTFJHT
display_name: Open journalism
created_by: benbalter
---
See how publications and data-driven journalists use open source to power their newsroom and ensure information is reported fairly and accurately.
 39  
collections/open-source-mlops/index.md
@@ -0,0 +1,39 @@
---
items:
 - microsoft/nni
 - magda-io/magda
 - milvus-io/milvus
 - feast-dev/feast
 - tobegit3hub/advisor
 - optuna/optuna
 - h2oai/h2o-3
 - MLReef/mlreef
 - SeldonIO/alibi
 - guildai/guildai
 - mlflow/mlflow
 - VertaAI/modeldb
 - bentoml/BentoML 
 - pycaret/pycaret
 - whylabs/whylogs
 - argoproj/argo-workflows
 - zenml-io/zenml
 - aimhubio/aim
 - interpretml/interpret
 - mlrun/mlrun
 - microsoft/pai
 - allegroai/clearml
 - iterative/dvc
 - determined-ai/determined
 - InfuseAI/primehub
 - treeverse/lakeFS
 - activeloopai/Hub
 - Netflix/metaflow
 - flyteorg/flyte
 - SchedMD/slurm
 - ray-project/ray
 - tensorflow/tensorboard
display_name: Open source MLOps
created_by: ManeSah
image: open-source-mlops.png
---
Open source projects to enhance your MLOps stack.
 BIN +218 KB 
collections/open-source-mlops/open-source-mlops.png

 36  
collections/open-source-organizations/index.md
@@ -0,0 +1,36 @@
---
items:
 - adobe/adobe.github.com
 - RedHatOfficial/RedHatOfficial.github.io
 - cfpb/cfpb.github.io
 - Netflix/netflix.github.com
 - Esri/esri.github.io
 - square/square.github.io
 - twitter/opensource-website
 - guardian/guardian.github.com
 - Yelp/yelp.github.io
 - Shopify/shopify.github.com
 - IBM/ibm.github.io
 - microsoft/microsoft.github.io
 - artsy/artsy.github.io
 - OSGeo/osgeo
 - godaddy/godaddy.github.io
 - cloudflare/cloudflare.github.io
 - eleme/eleme.github.io
 - didi/didi.github.io
 - alibaba/alibaba.github.com
 - google/google.github.io
 - proyecto26/proyecto26.github.io
 - mozilla/mozilla.github.io
 - zalando/zalando.github.io
 - stripe/stripe.github.io
 - newrelic/opensource-website
 - docker/docker.github.io
 - ExpediaGroup/expediagroup.github.io
 - wikimedia/wikimedia.github.io
 - fairfield-programming/fairfield-programming.github.io
display_name: Open source organizations
created_by: benbalter
image: open-source-organizations.png
---
A showcase of organizations showcasing their open source projects.
 BIN +101 KB 
collections/open-source-organizations/open-source-organizations.png

 13  
collections/php-frameworks/index.md
@@ -0,0 +1,13 @@
---
items:
 - laravel/laravel
 - symfony/symfony
 - bcit-ci/CodeIgniter
 - slimphp/Slim
 - yiisoft/yii
 - cakephp/cakephp
 - DinoPHP/DinoPHP
display_name: PHP Frameworks
created_by: Ahmed-Ibrahimm
---
While the number of ways to organize PHP is almost infinite, here are some frameworks that help you build clean applications.
 24  
collections/pixel-art-tools/index.md
@@ -0,0 +1,24 @@
---
items:
 - aseprite/aseprite/
 - piskelapp/piskel/
 - jvalen/pixel-art-react/
 - maierfelix/poxi/
 - gmattie/Data-Pixels/
 - vsmode/pixel8
 - kitao/pyxel
 - jackschaedler/goya
 - cloudhead/rx
 - Orama-Interactive/Pixelorama
 - LibreSprite/LibreSprite
 - lospec/pixel-editor
 - rgab1508/PixelCraft
 - PixiEditor/PixiEditor
 - Kully/pixel-paint
 - pulkomandy/grafx2/
 - pixa-pics/pixa-pics.github.io
display_name: Pixel Art Tools
created_by: leereilly
image: pixel-art-tools.png
---
Creating pixel art for fun or animated sprites for a game? The digital artist in you will love these apps and tools!
 BIN +2.29 KB 
collections/pixel-art-tools/pixel-art-tools.png

 21  
collections/policies/index.md
@@ -0,0 +1,21 @@
---
items:
 - github/balanced-employee-ip-agreement
 - github/site-policy
 - hackdaymanifesto/site
 - BetaNYC/Bike-Share-Data-Best-Practices
 - project-open-data/project-open-data.github.io
 - usds/playbook
 - 18F/open-source-policy
 - WhiteHouse/fitara
 - GSA/https
 - CommerceGov/Policies-and-Guidance
 - github/site-policy
 - Medium/medium-policy
 - Automattic/legalmattic
 - divegeek/uscode
 - seriesseed/equity
display_name: Policies
created_by: benbalter
---
From federal governments to corporations to student clubs, groups of all sizes are using GitHub to share, discuss, and improve laws.  *Ask not what the repository can do for you...*
 59  
collections/probot-apps/index.md
@@ -0,0 +1,59 @@
---
items:
 - https://probot.github.io/apps/wip/
 - https://probot.github.io/apps/stale/
 - https://probot.github.io/apps/dco/
 - https://probot.github.io/apps/todo/
 - https://probot.github.io/apps/welcome/
 - https://probot.github.io/apps/reminders/
 - https://probot.github.io/apps/first-timers/
 - https://probot.github.io/apps/settings/
 - https://probot.github.io/apps/request-info/
 - https://probot.github.io/apps/polls/
 - https://probot.github.io/apps/delete-merged-branch/
 - https://probot.github.io/apps/unfurl-links/
 - https://probot.github.io/apps/no-response/
 - https://probot.github.io/apps/lock/
 - https://probot.github.io/apps/pull/
 - https://probot.github.io/apps/release-drafter/
 - https://probot.github.io/apps/move/
 - https://probot.github.io/apps/dep/
 - https://probot.github.io/apps/sentiment-bot/
 - https://probot.github.io/apps/commitlint/
 - https://probot.github.io/apps/prevent-public-repos/
 - https://probot.github.io/apps/triage-new-issues/
 - https://probot.github.io/apps/semantic-pull-requests/
 - https://probot.github.io/apps/weekly-digest/
 - https://probot.github.io/apps/support/
 - https://probot.github.io/apps/reaction/
 - https://probot.github.io/apps/update-docs/
 - https://probot.github.io/apps/pr-triage/
 - https://probot.github.io/apps/ooo/
 - https://probot.github.io/apps/auto-assign/
 - https://probot.github.io/apps/mergeable/
 - https://probot.github.io/apps/invite-contributors/
 - https://probot.github.io/apps/linter-alex/
 - https://probot.github.io/apps/helpr/
 - https://probot.github.io/apps/eslint-disable/
 - https://probot.github.io/apps/remove-outside-collaborators/
 - https://probot.github.io/apps/issuelabeler/
 - https://probot.github.io/apps/background-check/
 - https://probot.github.io/apps/yamburger/
 - https://probot.github.io/apps/close-issue/
 - https://probot.github.io/apps/similar-code-searcher/
 - https://probot.github.io/apps/gitpod/
 - https://probot.github.io/apps/untrivializer/
 - https://probot.github.io/apps/minimum-reviews/
 - https://probot.github.io/apps/auto-comment/
 - https://probot.github.io/apps/profanity/
 - https://probot.github.io/apps/markdownify/
 - https://probot.github.io/apps/duplicator/
 - https://probot.github.io/apps/tap-release/
 - https://probot.github.io/apps/issue-complete/
 - https://probot.github.io/apps/deploy/
 - https://probot.github.io/apps/tests-checker/
display_name: Probot apps
created_by: brandonrosage
image: probot-apps.png
---
Use these pre-built apps to extend GitHub and automate your workflow.
 BIN +42.2 KB 
collections/probot-apps/probot-apps.png

 22  
collections/productivity-tools/index.md
@@ -0,0 +1,22 @@
---
items:
 - ohmyzsh/ohmyzsh
 - microsoft/terminal
 - ggreer/the_silver_searcher
 - arc90/git-sweep
 - bhollis/jsonview
 - ShareX/ShareX
 - sindresorhus/quick-look-plugins
 - rtyley/bfg-repo-cleaner
 - mhagger/git-imerge
 - eddiezane/lunchy
 - awaescher/RepoZ
 - sharkdp/fd
 - BurntSushi/ripgrep
 - stedolan/jq
 - alacritty/alacritty
 - ajeetdsouza/zoxide
display_name: Software productivity tools
created_by: holman
---
Build software faster with fewer headaches, using these tools and tricks
 72  
collections/programming-languages/index.md
@@ -0,0 +1,72 @@
---
items:
- golang/go
- apple/swift
- microsoft/TypeScript
- rust-lang/rust
- JetBrains/kotlin
- python/cpython
- php/php-src
- JuliaLang/julia
- ruby/ruby
- jashkenas/coffeescript
- elixir-lang/elixir
- crystal-lang/crystal
- PowerShell/PowerShell
- scala/scala
- dotnet/roslyn
- clojure/clojure
- micropython/micropython
- erlang/otp
- nim-lang/Nim
- AssemblyScript/assemblyscript
- purescript/purescript
- elm/compiler
- dotnet/csharplang
- red/red
- ponylang/ponyc
- Frege/frege
- goby-lang/goby
- racket/racket
- idris-lang/Idris-dev
- ocaml/ocaml
- typelead/eta
- programming-nu/nu
- gkz/LiveScript
- IoLanguage/io
- dlang/dmd
- terralang/terra
- dotnet/fsharp
- skiplang/skip
- rakudo/rakudo
- chapel-lang/chapel
- lucee/Lucee
- eclipse/golo-lang
- gosu-lang/gosu-lang
- ziglang/zig
- HaxeFoundation/haxe
- livecode/livecode
- coq/coq
- vlang/v
- dart-lang/sdk
- pharo-project/pharo
- ring-lang/ring
- SenegalLang/Senegal
- objectionary/eo
- ChavaScript/chavascript
- DennisMitchell/jellylanguage
- beefytech/Beef
- cue-lang/cue
- openjdk/jdk
- TinyCC/tinycc
- robhagemans/pcbasic
- QB64Team/qb64
- gleam-lang/gleam
- Gwion/Gwion
- vlang/v
- carbon-language/carbon-lang
- imba/imba
display_name: Programming languages
created_by: leereilly
---
A list of programming languages that are actively developed on GitHub
 41  
collections/projects-that-power-github/index.md
@@ -0,0 +1,41 @@
---
items:
 - bcrypt-ruby/bcrypt-ruby
 - zeroclipboard/zeroclipboard
 - github/resque
 - leereilly/swot
 - mysql/mysql-server
 - Leaflet/Leaflet
 - facebook/flow
 - chaijs/chai
 - primer/css
 - primer/octicons
 - eslint/eslint
 - mochajs/mocha
 - lerna/lerna
 - github/linguist
 - elastic/elasticsearch
 - rails/rails
 - redis/redis
 - rails/sprockets
 - libgit2/libgit2
 - libgit2/rugged
 - gjtorikian/html-pipeline
 - github/gemoji
 - jekyll/jekyll
 - octokit/octokit.rb.mn ::
Blog
About
Comparing main...patch-1 · zakwarlord7/INTERNAL
