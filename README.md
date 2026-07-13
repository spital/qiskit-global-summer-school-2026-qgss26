# Searchable quick docs for QGSS '26

**Status:** proposal for discussion with the mentor team  
**Recommendation:** GitHub Pages + Material for MkDocs, backed by a public GitHub repository  
**First principle:** publish a curated FAQ, not a redacted Discord archive

## Short answer

Yes—generating a documentation site from a GitHub repository should be a quick, inexpensive, and maintainable solution for this use case.

GitHub Pages is the hosting layer; it does not by itself turn Markdown into a particularly good searchable knowledge base. I recommend using **Material for MkDocs** to build the site. It gives us good navigation and full-text search that runs in the student's browser, so the initial version needs no search server, database, user account, or third-party search service. GitHub Actions can rebuild and publish the site after every approved change.

This also fits mentor collaboration well:

- content remains ordinary Markdown;
- mentors and students can propose changes through issues;
- mentors can review changes through pull requests;
- every answer has a stable URL that can be pasted into Discord;
- changes have history and named reviewers;
- the content can later move to another host without a large migration.

The extra setup compared with a GitHub wiki is small and pays for a much better newcomer experience. A useful first version could be launched with a small number of carefully chosen pages and roughly 15–30 real, recurring questions. It does not need to be complete before it is useful.

## The proposed shape

```text
Discord question
      |
      v
temporary private/local notes
      |
      v
rewrite as a general, self-contained Q&A
      |
      v
privacy + technical review in a pull request
      |
      v
public searchable documentation on GitHub Pages
```

Raw chat should never enter the public repository, a public issue, a pull request, a build log, or an external LLM prompt. A mentor should extract the underlying question and write a fresh answer in general language. This is safer and usually produces much better documentation than preserving the conversational transcript.

## Tool choice

| Option | Strengths | Drawbacks | Verdict |
| --- | --- | --- | --- |
| **Material for MkDocs + GitHub Pages** | Excellent documentation UX; built-in client-side search; Markdown; easy local preview; strong navigation; no search backend | Needs a small Python build and GitHub Actions workflow | **Recommended** |
| **Just the Docs + GitHub Pages** | Very small Jekyll setup; client-side search; close to the native GitHub Pages ecosystem | Less polished search/navigation; Ruby/Jekyll customization can become awkward | Good choice if minimizing the build setup is the overriding goal |
| **TiddlyWiki** | Fast and flexible for one person's nonlinear notes; can be used offline | A single-file workflow is less pleasant for concurrent edits, pull-request review, diffs, and merge conflicts | Better as a personal notebook than the team's public FAQ |
| **Docusaurus, VitePress, or a custom web app** | Powerful and highly customizable | More JavaScript/tooling and more decisions than this first version needs | Reconsider only if requirements outgrow MkDocs |
| **GitHub wiki or plain repository Markdown** | Almost no setup | Weaker navigation, public-facing presentation, and search experience | Useful as a temporary draft, not the desired newcomer entry point |

Material for MkDocs' search index is built from the generated pages, downloaded by the browser, and searched locally with no server. It supports search suggestions, highlighting, shareable search links, ranking boosts, and exclusions. That is enough “intelligence” for the first version and keeps operations and privacy simple.

For a very large documentation collection, a static search tool such as Pagefind could later be evaluated. It is unnecessary for the expected FAQ size.

## Suggested information architecture

Avoid one enormous “FAQ” page. Search results and shared links work better when questions live under short, task-oriented pages.

```text
docs/
  index.md                    # Start here; what this site is and is not
  getting-started.md          # The shortest successful path
  accounts-and-access.md
  local-setup.md
  course-navigation.md
  faq/
    installation.md
    notebooks-and-runtime.md
    qiskit-and-python.md
    submissions.md
    common-errors.md
  troubleshooting.md          # A symptom -> check -> fix path
  glossary.md
  asking-for-help.md           # What information to include safely
  privacy-and-content-policy.md
```

The home page should answer three things immediately:

1. What should I do first?
2. Where should I search when something fails?
3. Where do I ask when the answer is missing or my case needs a person?

Each FAQ entry should use the words a newcomer is likely to search for, including common synonyms and the exact safe portion of an error message. A consistent answer template will improve both reading and search quality:

```markdown
## Why does ...?

Short answer in one or two sentences.

### Try this

Numbered steps that lead to a verifiable result.

### If it still fails

Two or three common causes and what diagnostic information is safe to share.

### Applies to

QGSS '26; last verified YYYY-MM-DD.

### Sources

Links to the relevant official documentation.
```

Prefer one canonical answer and link to it from related pages instead of maintaining copies. Course-year-specific material should be labelled clearly and archived after the event; evergreen material can remain shared.

## Discord-to-FAQ privacy and security policy

“We removed the display names” is not sufficient anonymization. An unusual sentence, exact timestamp, code fragment, repository link, or error output may still identify its author or reveal a secret. The safest workflow is **data minimization plus synthesis**:

1. Read the recent Discord discussion in its authorized context.
2. Record only the general question and the validated resolution in a temporary private/local place.
3. Rewrite both in fresh, context-independent language; do not paste the transcript and then try to erase pieces.
4. Remove information that is not required to solve the general problem.
5. Have another mentor review privacy and technical correctness.
6. Delete the temporary raw material according to an agreed short retention period.
7. Publish only the reviewed Markdown.

Before publication, check especially for:

- real names, Discord handles, user/server/channel IDs, avatars, and timestamps;
- email addresses, phone numbers, private repository or notebook URLs, and invite links;
- API keys, access tokens, cookies, authorization headers, connection strings, and recovery codes;
- URLs with sensitive path segments or query parameters;
- local paths containing usernames, IP addresses, hostnames, device IDs, and cloud resource IDs;
- screenshots, image metadata, notebook outputs, stack traces, and code comments containing any of the above;
- grades, accommodations, health information, disputes, or other personal circumstances;
- unpublished course material or answers that students are not allowed to share.

Automated secret scanning should be added as defence in depth, locally before commit and in CI, but a clean scan is not privacy approval. If a real credential is ever committed, revoke or rotate it immediately; merely deleting it in a later commit is not enough because Git clones and history contain prior versions.

Before collecting material, the mentor team should agree with the program owner on scope, attribution, participant expectations, retention, and who handles removal requests. If an exact quotation or attributed contribution is genuinely useful, obtain explicit permission for that specific publication. Otherwise, paraphrase and do not attribute it.

Finally, assume that both the repository and the generated site are public. Standard GitHub Pages should not be treated as access-controlled storage; private publication is an Enterprise Cloud feature. Public GitHub issues and Actions logs are public surfaces too.

## Collaboration model

Put the repository under a team or program organization if possible, rather than under one mentor's personal account. Use a protected default branch and require at least one mentor review for content changes. A small `CODEOWNERS` file can route changes to the active maintainers.

Enable an “Edit this page” link on every page and create structured issue forms for:

- **Missing question:** “I searched for X but could not find it.”
- **Correction or stale answer:** include the page URL and what has changed.
- **Navigation/search problem:** include the search words used and the expected page.

Every form should state prominently:

> This issue is public. Do not paste Discord messages, names, email addresses, tokens, private links, grades, or screenshots containing personal information.

Provide a separate private contact route for accidental disclosure, safeguarding concerns, account-specific access problems, and removal requests. Do not ask people to report sensitive mistakes through a public issue.

Useful initial labels would be `new-faq`, `correction`, `stale`, `navigation`, `privacy-review`, `mentor-review`, and `good-first-contribution`. Keep ownership simple: one mentor triages new issues, the relevant topic mentor validates the answer, and a second mentor approves publication.

The social loop is as important as the site:

1. Pin the single documentation link in the main Discord help channels.
2. When a question repeats, answer briefly and include the canonical deep link.
3. If the page is missing or was hard to find, open an issue rather than answering indefinitely only in Discord.
4. Review repeated Discord questions and open issues at a short, regular mentor meeting.
5. Archive or update pages when the course environment changes.

This makes Discord the conversation and escalation channel, while the site becomes the durable source of reviewed answers.

## Minimal implementation

The first repository version only needs:

```text
docs/
mkdocs.yml
requirements.txt (with a pinned major version)
.github/workflows/docs.yml
.github/ISSUE_TEMPLATE/
CONTRIBUTING.md
CODEOWNERS
LICENSE
```

Recommended initial configuration:

- Material for MkDocs with the built-in `search` plugin;
- search suggestions, result highlighting, and shareable search URLs;
- repository, issue, and “edit this page” links;
- a strict build in CI so broken navigation fails before deployment;
- a link checker and secret scanner in CI;
- no analytics, advertising, third-party comments, remote embeds, or custom JavaScript initially;
- HTTPS on the standard `github.io` address; a custom domain can wait;
- pinned dependencies and periodic, reviewed dependency updates.

GitHub Pages can deploy the generated static site using a custom Actions workflow. Give the deployment job only the documented minimum permissions, and review third-party Actions like any other dependency.

### Launch sequence

1. Obtain program approval for a public site and adopt the content/privacy policy.
2. Initialize the repository and deploy a nearly empty skeleton.
3. Seed it with the newcomer path and the top 15–30 repeated questions.
4. Ask two or three mentors and a few newcomers to find real answers without guidance.
5. Fix vocabulary, navigation, and missing results observed in that test.
6. Publish the single link in Discord and start the issue/PR feedback loop.

Good early success measures are modest: a newcomer can reach the first successful setup, common answers are found in under a minute, mentors increasingly reply with stable links, stale pages receive owners, and no personal or secret material is published. Avoid analytics at launch. A short opt-in feedback link and issue counts are enough until there is a specific question that requires more measurement.

## About a future in-browser LLM

Do not make an LLM a dependency for the initial site. Good structure, vocabulary, and search will solve most of the problem with almost no device or privacy cost.

If an in-browser assistant is tested later, it should:

- be explicitly opt-in before model weights are downloaded;
- show the approximate download size and device/browser requirements first;
- leave normal navigation and search fully usable without it;
- retrieve only from the reviewed public documentation, never raw Discord exports;
- cite and link the exact documentation passages used in an answer;
- say when the documentation does not contain an answer;
- avoid sending questions or documents to a remote service without separate, informed consent;
- have a versioned model and a simple way to remove cached weights.

The quality of that assistant will depend more on a clean, current, well-structured corpus than on adding the model early. Building the docs first is therefore not throwaway work; it is the foundation for any later retrieval or local-LLM experiment.

## Decision

Proceed with a **public GitHub repository, Material for MkDocs, client-side search, and GitHub Pages deployment**. Treat the repository as a collaborative publication, not a chat archive. Start small, use synthesized answers, require mentor review, and make privacy warnings part of every contribution route.

If the team wants the absolute smallest possible build setup, use **Just the Docs** instead; the content and governance recommendations above remain the same. TiddlyWiki is not the preferred shared-authoring format for this project.

## References

- [Material for MkDocs: setting up site search](https://squidfunk.github.io/mkdocs-material/setup/setting-up-site-search/)
- [Material for MkDocs: built-in search plugin](https://squidfunk.github.io/mkdocs-material/plugins/search/)
- [Material for MkDocs: ensuring data privacy](https://squidfunk.github.io/mkdocs-material/setup/ensuring-data-privacy/)
- [GitHub Docs: using custom workflows with GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)
- [GitHub Docs: configuring a publishing source](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)
- [GitHub Docs: changing the visibility of a GitHub Pages site](https://docs.github.com/en/enterprise-cloud@latest/pages/getting-started-with-github-pages/changing-the-visibility-of-your-github-pages-site)
- [GitHub Docs: configuring issue templates and forms](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/configuring-issue-templates-for-your-repository)
- [Just the Docs: search](https://just-the-docs.com/docs/search/)

References and tool behavior checked on 2026-07-10.
