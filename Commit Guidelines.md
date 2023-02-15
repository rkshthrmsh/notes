# Commit Guidelines
#git 

Each commit should be a logically separate changeset. The staging area can be used to separate large chunks of changes to digestible commits, each with its own commit message. Files can be staged partially using Interactive Staging. Rewriting History is another technique to clean up the commit history to make the end result more tractable.

Most importantly, commit messages need to follow a standard high-quality template to ensure that code revisit and collaboration become easier. As a general rule, ensure that the commit messages are composed of a summary line (or title) that captures the purpose of the changeset concisely and then a detailed explanation including the motivation for the change and a comparison with the previous implementation. Finally commit messages should be written in the imperative: "Fix bug" instead of "Fixed bug" or "Fixes bug".

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