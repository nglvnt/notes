# About commits and commit messages

## Table of contents <!-- omit in toc -->

- [About commits and commit messages](#about-commits-and-commit-messages)
  - [Code, context, commit](#code-context-commit)
  - [Commit style](#commit-style)
    - [Recommendations](#recommendations)
  - [Antipatterns](#antipatterns)
  - [Commit message models](#commit-message-models)
    - [Tim Pope I](#tim-pope-i)
    - [Tim Pope II](#tim-pope-ii)
    - [Chris Beams](#chris-beams)
    - [Subsurface](#subsurface)
  - [Resources](#resources)
    - [Further resources](#further-resources)

## Code, context, commit

Reestablishing code context is crucial when we have to modify or debug existing code or add a new feature to a codebase, hence we should make it as simple as possible. Commit messages can naturally serve as a convenient medium to communicate this context to co-developers (including our future selves): the diff can tell about how the code has been changed, but the commit message can explain why the code has been changed.

To provide the context, a good commit message should:

- **explain the problem the change tries to solve and justify the necessity of the change**: it may fix a bug, add a new feature, offer improvement in some aspect (performance, reliability, stability, etc.);
- **describe how the issue is handled, giving a high-level summary of the approach**. For small and/or obvious changes this might be omitted. Discarded alternate approaches could also be detailed;
- **tell about the results and effects** (not just the obvious ones, but also benchmarks, side effects) **of the change**, the ways in which the new behaviour is better.

When these are supplied, they

- make collaboration with co-developers (including our future selves) smoother,
- help reviewers to decide whether the suggested approach is correct,
- help maintainers to see whether the change is suitable to be included in production.

In this way, a good commit message contributes to maintainability and hence the long-term success of the project.

## Commit style

The following is a list of recommendations about the *contents* and the *form* of a commit. When a team settles on a guideline (even if it differs from the one given below), both of these aspects should be considered in order to get a coherent result.

### Recommendations

1. **Make separate commits for logically separate changes.**

    A change should be **atomic**: it adds one new feature, fixes one bug, solves one particular task.

    This way it is easier to pull out or revert the change without affecting other changes or to merge the change to other branches. It also makes the diffs more transparent: the smaller the code change, the easier and faster to review it. Moreover, atomic changes can alleviate the usage of `git bisect` or `git blame`.

    In general, it is better to have more commits than too few.

2. **The commit message should explain the intent and the approach, not the code.**

    Always state the original problem explicitly in the commit message. For example: describing a bug is not enough, as it is just an artifact of how the problem was recognized, the message should contain the root cause of the bug.

    Describe the motivation (the *why?*), tell about the new behaviour versus the old one (the *what?*) and give reasons on the chosen way the issue is solved (abandoned alternative approaches could be detailed as well). Improvement opportunities, known limitations, or compromises between short term goals vs. long term wishes might be also included in the commit message. When manual tests were performed, the test case titles could be enumerated in the commit message, too.

    The code usually explains itself (the *how?*), but if required, complicated parts may be commented in the source.

    Try to make the commit message self-contained, e.g. do not just give a link to an issue/ticket, but outline the main points of its discussion. An online issue tracker or documentation might not be accessible all the time, however, Git stores all of its data locally, a feature we can put to good use.

    A lengthy commit message might indicate that the commit is not *atomic*, see **Make separate commits for logically separate changes.** In this case, splitting the commit is the best choice.

    In summary:

    > The commit message must contain all the information required to fully understand & review the patch for correctness. Less is not more. More is more.
    >
    > -- [OpenStack Git Commit Good Practice](https://wiki.openstack.org/wiki/GitCommitMessages)

3. **Split the commit message to a subject and a body, separating them with a blank line.**

    Git treats the text before the first blank line as the commit subject (also called title), which is used by many Git tools, e.g. `git format-patch`. The first line is also what is mostly seen, make sure that it is as good as possible.

    The *subject* should be a single short line summarizing the change.

    The *body*, if needed, should contain the more detailed description, possibly spanning multiple paragraphs, divided by blank lines. It is okay to have a subject-only commit message, for example when the patch is simple and no further context is required.

    A last part, a *footer* could also be used, where commit-related metadata could be shared: issue tracker system IDs, pull request numbers might be added here or the different contributors of the commit (e.g. reporter, reviewer, tester, etc.) might be listed.

4. **It is best to limit the length of the subject line.**

    The maximal length is usually taken to be between 50 and 72 characters. For instance, GitHub warns when the length of the subject reaches 50 characters when using the web editor, and truncates a subject line longer than 72 characters with an ellipsis when the subject is displayed.

    Limiting the length also makes the author to be to the point. If brief explanation seems difficult, then the commit might not be *atomic*, see **Make separate commits for logically separate changes.** In this case `git add --patch` might be used to split the commit up.

5. **Limiting the line length of the body** is also recommended.

    Git never wraps text automatically, hence text should be wrapped manually when writing the body. The suggestion is to have a limit of 72 characters, so that Git has plenty of room to indent text while still keeping everything under 80 characters overall.

6. **The subject line should be capitalized and should not end with a period.**

    Even a period can make the subject longer than prescribed limit.

7. **Use the imperative mood in the subject line.**

    Imperative mood means “spoken or written as if giving a command or instruction”. One reason is that Git itself uses the imperative mood whenever it creates a commit on your behalf. A properly formed Git commit subject line should always be able to complete the following sentence: *If applied, this commit will (your subject line here).*

8. **The body should be in present tense**, but imperative mood is not necessary.

9. **Checking for whitespace errors before committing** is also suggested.

    One can run `git diff --check`, which identifies and lists possible whitespace errors.

## Antipatterns

The following is a collection of antipatterns best to be avoided:

1. **End-of-the-day commits**: committing all the changes across the whole codebase in a single commit at the end of the day/week/sprint/some time period. This results in commits that are hard to understand and work with.
2. **One file - one commit**: if multiple files are effected by a logical change, then they should go to the same commit.
3. **Multiple, unrelated changes in one commit**: this results in commits that are hard to understand and work with.
4. **One large change in one large commit**: if a change can be divided to smaller sequence of commits, then it should be split. As quoted above: Less is not more. More is more.
5. **Committing whitespace changes with code changes**: a code change can be easily overlooked among whitespace changes. Some tools allow the hiding of whitespace changes though, a useful feature when encountering a situation like this.
6. **Writing general commit messages**: messages like "Change code", "Fix bug", "Add new feature" are valueless and unhelpful.
7. **Not using a commit message style guide**: even if it is an existing project, it is never too late to start using a guideline. The advantages of having a clean and structured commit history will pay off in the long run.
8. **Initial commit**: strictly speaking, the commit message "Initial commit" does not adhere to the recommendation **Use the imperative mood in the subject line**, hence it should not be used. One alternative might be "Initialize repository". For creating an empty commit, one can use the `--allow-empty` option of `git commit`. However, if the initial commit is not empty (e.g. a README is created), then an even better alternative is to describe the initial change itself.

## Commit message models

### Tim Pope I

An example written by [Tim Pope](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html):

```text
Capitalized, short (50 chars or less) summary

More detailed explanatory text, if necessary.  Wrap it to about 72
characters or so.  In some contexts, the first line is treated as the
subject of an email and the rest of the text as the body.  The blank
line separating the summary from the body is critical (unless you omit
the body entirely); tools like rebase will confuse you if you run the
two together.

Write your commit message in the imperative: "Fix bug" and not "Fixed bug"
or "Fixes bug."  This convention matches up with commit messages generated
by commands like git merge and git revert.

Further paragraphs come after blank lines.

- Bullet points are okay, too

- Typically a hyphen or asterisk is used for the bullet, followed by a
  single space, with blank lines in between, but conventions vary here

- Use a hanging indent
```

### Tim Pope II

And an other one by [Tim Pope](https://commit.style/):

```text
Commit message style guide for Git

The first line of a commit message serves as a summary.  When displayed
on the web, it's often styled as a heading, and in emails, it's
typically used as the subject.  As such, you should capitalize it and
omit any trailing punctuation.  Aim for about 50 characters, give or
take, otherwise it may be painfully truncated in some contexts.  Write
it, along with the rest of your message, in the imperative tense: "Fix
bug" and not "Fixed bug" or "Fixes bug".  Consistent wording makes it
easier to mentally process a list of commits.

Oftentimes a subject by itself is sufficient.  When it's not, add a
blank line (this is important) followed by one or more paragraphs hard
wrapped to 72 characters.  Git is strongly opinionated that the author
is responsible for line breaks; if you omit them, command line tooling
will show it as one extremely long unwrapped line.  Fortunately, most
text editors are capable of automating this.
```

### Chris Beams

An extension of the first one by [Chris Beams](https://cbea.ms/git-commit/):

```text
Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
   by a single space, with blank lines in between, but conventions
   vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789
```

### Subsurface

Another example from the [Subsurface project](https://github.com/subsurface/subsurface/blob/master/CONTRIBUTING.md), originally written by [Linus Torvalds](https://github.com/subsurface/subsurface/commit/b6590150d68df528efd40c889ba6eea476b39873#diff-2b7814d3fca2e99e56c51b6ff2aa313ea6e9da6424804240aa8ad891fdfe0900) and extended by [Thiago Macieira](https://github.com/subsurface/subsurface/commit/99b8e85d739387c0c8342535b28e010cdac74a5f#diff-2b7814d3fca2e99e56c51b6ff2aa313ea6e9da6424804240aa8ad891fdfe0900).

```text
Header line: explain the commit in one line (use the imperative)

Body of commit message is a few lines of text, explaining things
in more detail, possibly giving some background about the issue
being fixed, etc etc.

The body of the commit message can be several paragraphs, and
please do proper word-wrap and keep columns shorter than about
74 characters or so. That way "git log" will show things
nicely even when it's indented.

Make sure you explain your solution and why you're doing what you're
doing, as opposed to describing what you're doing. Reviewers and your
future self can read the patch, but might not understand why a
particular solution was implemented.

Reported-by: whoever-reported-it
Signed-off-by: Your Name <you@example.com>
```

## Resources

Chris Beams' blog post: <https://cbea.ms/git-commit/>

Tim Pope's note: <https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html>

Tim Pope's other commit style model: <https://commit.style/>

Pro Git (2nd ed) Commit guidelines: <https://www.git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project#_commit_guidelines>

Git commit documentation discussion: <https://git-scm.com/docs/git-commit#_discussion>

Git patch submission guidelines: <https://github.com/git/git/blob/master/Documentation/SubmittingPatches>

Peter Hutterer's blog post: <http://who-t.blogspot.com/2009/12/on-commit-messages.html>

Subsurface contributing guidelines: <https://github.com/subsurface/subsurface/blob/master/CONTRIBUTING.md>

Erlang/OTP good commit messages guidelines: <https://github.com/erlang/otp/wiki/writing-good-commit-messages>

Spring Projects contributing guidelines: <https://github.com/spring-projects/spring-framework/blob/30bce7/CONTRIBUTING.md#format-commit-messages>

Fresh Consulting blog post about atomic comments: <https://www.freshconsulting.com/insights/blog/atomic-commits/>

OpenStack Git Commit Good Practice: <https://wiki.openstack.org/wiki/GitCommitMessages>

McQuaid - Git in Practice, Manning, 2014. <https://www.manning.com/books/git-in-practice>

### Further resources

<https://stackoverflow.com/questions/2290016/git-commit-messages-50-72-formatting>

<https://stackoverflow.com/questions/16122234/how-to-commit-a-change-with-both-message-and-description-from-the-command-li>

<https://www.conventionalcommits.org/en/v1.0.0/>

<https://gist.github.com/robertpainsi/b632364184e70900af4ab688decf6f53>

<https://dev.to/i5han3/git-commit-message-convention-that-you-can-follow-1709>

<https://ec.europa.eu/component-library/v1.15.0/eu/docs/conventions/git/>

<https://udacity.github.io/git-styleguide/>

<https://github.com/spring-projects/spring-framework/blob/30bce7/CONTRIBUTING.md#squash-commits>

<https://ericmjl.github.io/essays-on-data-science/workflow/effective-commit-messages/>

<https://xkcd.com/1296/>

<http://whatthecommit.com/>

<https://www.reddit.com/r/ProgrammerHumor/comments/375fjr/whats_the_most_humorous_commit_message_youve_ever/>

<https://geeksta.net/geeklog/exploring-expressions-emotions-github-commit-messages/>

<https://robertcooper.me/post/git-commit-messages>

<https://gist.github.com/develar/273e2eb938792cf5f86451fbac2bcd51>

<https://gist.github.com/abravalheri/34aeb7b18d61392251a2>

<https://gist.github.com/stephenparish/9941e89d80e2bc58a153>

<https://anglebrackets.dev/index.php/2020/02/25/tips-on-writing-good-git-commit-messages/>

<https://medium.com/@menuka/writing-meaningful-git-commit-messages-a62756b65c81>

<https://github.com/angular/angular/blob/master/CONTRIBUTING.md>

<https://nitayneeman.com/posts/understanding-semantic-commit-messages-using-git-and-angular/>

<https://medium.com/compass-true-north/writing-good-commit-messages-fc33af9d6321>

<https://herewecode.io/blog/a-beginners-guide-to-git-how-to-write-a-good-commit-message/>

<https://dev.to/herewecode/a-beginner-s-guide-to-git-how-to-write-a-good-commit-message-2j49>

<https://dev.to/herewecode/a-beginner-s-guide-to-git-what-is-a-changelog-and-how-to-generate-it-11e7>

<https://dev.to/puritanic/how-are-you-writing-a-commit-message-1ih7>

<https://visualstudiomagazine.com/articles/2013/06/01/roc-rocks.aspx>

<http://blog.joncairns.com/2015/09/use-git-to-comment-your-code/>

<https://agateau.com/2015/extensive-source-comments-or-extensive-commit-messages/>

<https://github.com/mristin/opinionated-commit-message>

<https://driggl.com/blog/a/how-to-write-professional-commits-efficiently>

<https://driggl.com/blog/a/how-squashing-commits-can-improve-your-git-workflow>

<https://hashnode.com/post/which-commit-message-convention-do-you-use-at-work-ck3e4jbdd00zyo4s1h7mc7e0g>

<https://www.freecodecamp.org/news/writing-good-commit-messages-a-practical-guide/>

<https://docs.gitlab.com/ee/development/contributing/merge_request_workflow.html>

<https://style.tidyverse.org/gitgithub.html>

<https://www.theserverside.com/video/Follow-these-git-commit-message-guidelines>

<https://awesomeopensource.com/project/slashsbin/styleguide-git-commit-message>

<https://marketplace.visualstudio.com/items?itemName=vivaxy.vscode-conventional-commits>

<https://jiewonchang1.gitbooks.io/udacity-summary/content/4-commit-messages.html>

<https://blog.tericcabrel.com/apply-conventional-commit-style-on-your-project-with-commitlint/>

<https://docs.gitlab.com/ee/user/project/merge_requests/commit_templates.html>

<https://ckeditor.com/docs/ckeditor5/latest/framework/guides/contributing/git-commit-message-convention.html>

<https://mokkapps.de/blog/how-to-automatically-generate-a-helpful-changelog-from-your-git-commit-messages/>

<https://reflectoring.io/meaningful-commit-messages/>

<https://chiamakaikeanyi.dev/how-to-write-good-git-commit-messages/>

<https://www.simplethread.com/what-makes-a-good-git-commit/>

<https://stackoverflow.com/questions/7425541/git-commit-style-all-changed-files-at-once-or-one-at-a-time>

<https://stackoverflow.com/questions/61262376/questions-about-conventional-commit-messages-in-git>

<https://www.eficode.com/blog/keep-your-git-commits-clean>

<https://commitlint.js.org/#/>

<https://softwareengineering.stackexchange.com/questions/52267/why-should-i-write-a-commit-message>

<https://softwareengineering.stackexchange.com/questions/254978/whats-wrong-with-comments-that-explain-complex-code>

<https://softwareengineering.stackexchange.com/questions/185923/how-can-i-deal-with-a-team-member-who-dislikes-making-comments-in-code>

<https://softwareengineering.stackexchange.com/questions/336430/coding-standard-for-clarity-comment-every-line-of-code>

<https://softwareengineering.stackexchange.com/questions/1/comments-are-a-code-smell>

<https://softwareengineering.stackexchange.com/questions/367094/my-client-wants-25-of-comments-in-my-current-project-how-to-react>

<https://stackoverflow.com/questions/184618/what-is-the-best-comment-in-source-code-you-have-ever-encountered>

<https://softwareengineering.stackexchange.com/questions/125320/do-todo-comments-make-sense>

<https://softwareengineering.stackexchange.com/questions/109523/how-do-i-convince-my-fellow-devs-to-want-to-add-comments-to-source-code-commits>

<https://softwareengineering.stackexchange.com/questions/207710/what-are-examples-of-comments-that-tell-you-why-instead-of-how-or-what>

<https://softwareengineering.stackexchange.com/questions/404737/how-to-avoid-comments-about-one-line-of-code-for-cleanliness>

<https://softwareengineering.stackexchange.com/questions/52833/is-writing-comments-inside-methods-not-a-good-practice>

<https://stackoverflow.com/questions/12396622/what-does-1-line-adds-whitespace-errors-mean-when-applying-a-patch/12396793>

<https://softwareengineering.stackexchange.com/questions/121555/why-is-trailing-whitespace-a-big-deal>

<https://softwareengineering.stackexchange.com/questions/190096/can-commented-out-code-be-valuable-documentation>

<https://softwareengineering.stackexchange.com/questions/285787/clean-code-comments-vs-class-documentation>

<https://github.com/dbt-labs/corp/blob/master/git-guide.md>

<https://github.com/platisd/bad-commit-message-blocker>

<https://dhwthompson.com/2019/my-favourite-git-commit>

<https://blog.mocoso.co.uk/talks/2015/01/12/telling-stories-through-your-commits/>

<https://tekin.co.uk/2019/02/a-talk-about-revision-histories>
