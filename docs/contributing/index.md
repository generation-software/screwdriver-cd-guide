---
layout: main
title: Contributing
category: Contributing
menu: menu
toc:
  - title: Contributing
    url: "#contributing"
    active: true
  - title: What to Work On
    url: "#what-to-work-on"
  - title: General Guidelines for Contributing
    url: "#general-guidelines-for-contributing"
  - title: Submitting Pull Requests
    url: "#submitting-pull-requests"
---

Contributing
============

Thank you for considering contributing! There are many ways you can help.

What to Work On
---------------

Screwdriver is a "distributed" project where each component sits in its own GitHub Repo. If you're not sure what
repositories you need to change, please refer to our [Where to Contribute doc](./where-to-contribute).
For pointers on developing, checkout the [Development docs](./development).

Our documentation, however, is centralized at

- [product page]
- [documentation]

General Guidelines for Contributing
-----------------------------------

Please try to:

- update [issues](./issues) you're working on with a daily summary
- open a Discord channel
  ![Discord](https://img.shields.io/discord/1194147154760048730?style=for-the-badge&logo=discord&logoColor=white&label=Discord&labelColor=5865F2&link=https%3A%2F%2Fdiscord.com%2Fwidget%3Fid%3D1194147154760048730)
  with the issue number and feature in the channel title (e.g. `#911-subdirectory-support`) and discuss work in there
- submit a [design doc](https://github.com/QubitPi/screwdriver-cd-screwdriver/tree/master/design) if applicable

Submitting Pull Requests
------------------------

Patches for fixes, features, and improvements are accepted through pull requests. Here are some tips for contributing:

- Write good commit messages in the present tense ("Add X", not "Added X") with a short title, blank line, and bullet
  points if needed. Capitalize the first letter of the title and any bullet items. No punctuation in the title.
- Code must pass lint and style checks.
- All external methods must be documented. Add README docs and/or user documentation in our [guide][guide-repo] when
  appropriate.
- Include tests to improve coverage and prevent regressions.
- Squash changes into a single commit per feature/fix. Ask if you're unsure how to discretize your work.
- Whenever possible, tag your pull request with appropriate Github labels.

_Please ask before embarking on a large improvement so you're not disappointed if it does not align with the goals of
the project or owner(s)._

[documentation]: https://screwdriver-docs.qubitpi.org/

[guide-repo]: https://github.com/screwdriver-cd/guide

[product page]: https://qubitpi.github.io/screwdriver-cd-homepage/
