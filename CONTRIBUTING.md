# Contributing to Charmed HPC's documentation

Do you want to contribute to Charmed HPC's documentation? You've come to
the right place then! __Here is how you can get involved.__

Please take a moment to review this document so that the contribution
process will be easy and effective for everyone. Also, please familiarise
yourself with MyST and Diátaxis as it will help you better understand how
Charmed HPC's documentation is put together.

Following these guidelines helps you communicate that you respect the maintainers
managing Charmed HPC's documentation. In return, they will reciprocate that respect
while addressing your issue or assessing your submitted changes and/or features.

Have any questions? Feel free to ask them in the [Ubuntu High-Performance Computing
Matrix chat](https://matrix.to/#/#hpc:ubuntu.com).

### Table of Contents

* [Using the issue tracker](#using-the-issue-tracker)
* [Issues and Labels](#issues-and-labels)
* [Bug Reports](#bug-reports)
* [Enhancement Proposals](#enhancement-proposals)
* [Pull Requests](#pull-requests)
* [Discussions](#discussions)
* [Documentation Guidelines](#documentation-guidelines)
* [Licensing](#licensing)

## Using the issue tracker

The issue tracker is the preferred way for tracking [bug reports](#bug-reports), [feature requests](#feature-requests),
and [submitted pull requests](#pull-requests), but please follow these guidelines for the issue tracker:

* Please __do not__ use the issue tracker for personal issues and/or support requests.
The [Discussions](#discussions) page is a better place to get help for personal support requests.

* Please __do not__ derail or troll issues. Keep the discussion on track and have respect for the other
users/contributors of the Slurm workload manager snap.

* Please __do not__ post comments consisting solely of "+1", ":thumbsup:", or something similar.
Use [GitHub's "reactions" feature](https://blog.github.com/2016-03-10-add-reactions-to-pull-requests-issues-and-comments/)
instead.
  * The maintainers of Charmed HPC's documentation reserve the right to delete comments
  that violate this rule.

* Please __do not__ repost or reopen issues that have been closed. Please either
submit a new issue or browser through previous issues.
  * The maintainers of Charmed HPC's documentation reserve the right to delete issues
  that violate this rule.

## Issues and Labels

The Charmed HPC documentation issue tracker uses a variety of labels to help organize
and identify issues. Here is a list of some of these labels, and how the maintainers of
Charmed HPC's documentation use them:

* `Type: Bug` - Issues within the Charmed HPC documentation that either produce errors, unexpected behavior, or cause users to encounter unintended errors.

* `Status: Confirmed` - Issues marked `Type: Bug` that have be confirmed to be reproducible on a separate system.

* `Type: Documentation` - Issues for revising or enhancing Charmed HPC's documentation.
Can also be used for pull requests.

* `Type: Refactor` - Issues that pertain to improving Charmed HPC's documentation.

* `Type: Enhancement` - Issues marked as an agreed upon enhancement to Charmed HPC's documentation. Can also be used for pull requests.

* `Status: Help Wanted` - Issues where we need help from the Ubuntu HPC community to solve.

For a complete look at Charmed HPC's documentation labels, see the
[project labels page](https://github.com/charmed-hpc/docs/labels).

## Bug Reports

A bug is a *demonstrable problem* that is caused by errors in Charmed HPC's
documentation. Good bug reports make Charmed HPC's documentation better, so
thankyou for taking the time to report issues!

Guidelines for reporting bugs with Charmed HPC's documentation:

1. __Validate your issue__ &mdash; ensure that your issue is not being caused by either
a semantic or syntactic error in your environment.

2. __Use the GitHub issue search__ &mdash; check if the issue you are encountering has
already been reported by someone else.

3. __Check if the issue has already been fixed__ &mdash; try to reproduce your issue
using the latest version of Charmed HPC's documentation.

4. __Isolate the problem__ &mdash; the more pinpointed the issue in the documentation
is, the easier it is to fix.

A good bug report should not leave others needing to chase you for more information.
Some common questions you should answer in your report include:

* What is your current environment?
* Were you able to reproduce the issue in another environment?
* Which steps from Charmed HPC's documentation produce the issue?
* What was your expected outcome?

Please try to be as detailed as possible in your report. All these details will help the
maintainers quickly fix issues with Charmed HPC's documentation.

## Enhancement Proposals

Enhancement proposals can be posted to the Charmed HPC documentation issue tracker.

The Charmed HPC team may already know what they want to included in the documentation,
but they are always open to new ideas and potential improvements. GitHub Discussions is
a good place for discussing open-ended questions that pertain to the entire Charmed HPC
project, but more focused enhancement proposal discussion can start within the issue
tracker.

Please note that not all proposals may be incorporated into the documentation. Please
know that spamming the maintainers to incorporate something you want into the documentation
will not improve the likelihood of being implemented; it may result in you receiving a
temporary ban from the repository.

## Pull Requests

Good pull requests &mdash; spelling corrections, revisions, new sections &mdash;
are a huge help. Pull requests should remain focused and not contain commits not
related to what you are contributing.

__Ask first__ before embarking on any __significant__ pull request such as add new sections,
changing the layout, or heavily revising existing sections; otherwise, you risk spending a
lot of time working on something that Charmed HPC's maintainers may not want to merge into the
documentation! For trivial changes, or contributions that do not require a large amount of time,
you can go ahead and make a pull request.

Adhering to the following process is the best way to get your contribution accepted into
Charmed HPC's documentation:

1. [Fork](https://help.github.com/articles/fork-a-repo/) the project, clone your fork,
   and configure the remotes:

   ```shell
   # Clone your fork of the repo into the current directory
   $ git clone git@github.com:<your-username>/docs.git

   # Navigate to the newly cloned directory
   $ cd docs

   # Assign the original repo to a remote called "upstream"
   $ git remote add upstream git@github.com:charmed-hpc/docs.git
   ```

2. If you cloned a the documentation a while ago, pull the latest changes from the
upstream Charmed HPC documentation repository:

   ```bash
   $ git checkout main
   $ git pull upstream main
   ```

3. Create a new topic branch off the main development branch to
   contain your feature, change, or fix:

   ```bash
   $ git checkout -b <topic-branch-name>
   ```

4. Ensure that your changes pass all tests:

    ```shell
    # Check links
    $ make linkcheck

    # Check spelling
    $ make spelling

    # Check inclusive language
    $ make woke

    # Check accessibility
    $ make pa11y

    # Ensure style guide compliance
    $ make vale
    ```

5. Commit your changes in logical chunks to your topic branch.

   Our project follows the
   [Conventional Commits specification, version 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/).
   You can use Git's
   [interactive rebase](https://help.github.com/articles/about-git-rebase/) feature to
   tidy up your commits before pushing them to your origin branch.

6. Locally merge or rebase the upstream development branch into your topic branch:

   ```shell
   git pull [--rebase] upstream main
   ```

7. Push your topic branch up to your fork:

   ```shell
   git push origin <topic-branch-name>
   ```

8. [Open a Pull Request](https://help.github.com/articles/about-pull-requests/)
    with a clear title and description against the `main` branch.

> By submitting your changes to Charmed HPC's documentation, you agree to license your
> contributions under the terms of the [Creative Commons Attribution-Share Alike
> International license, version 4.0](./LICENSE).

## Discussions

GitHub Discussions is a great place to connect with other Charmed HPC users to
discuss potential enhancements, ask questions, and resolve issues. Charmed HPC users
should remain respectful of each other. Discussion moderators reserve the right to
suspend discussions and/or delete posts that do not follow this rule.

## Documentation guidelines

We use [Markedly Structured Text (MyST)](https://mystmd.org/guide/typography) to
write our documentation, and we follow [Diátaxis](https://diataxis.fr) for
developing and guiding our documentation practice.

Interested in learning how to contribute to open source documentation?
Check out [Canonical's Open Documentation Academy](https://canonical.com/documentation/open-documentation-academy)
and how they can help you level up your contributions!


## Licensing

By contributing your changes to Charmed HPC's documentation, you agree to license
your contribution under the [Creative Commons Attribution-Share Alike International license,
version 4.0](./LICENSE).