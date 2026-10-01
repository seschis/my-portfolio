# my-portfolio

Personal developer portfolio, resume, and blog.

**[👉 Visit the live site](https://seschis.github.io/my-portfolio/)**

## Stack

- [Hugo](https://gohugo.io/) (extended) with the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme
- [GitHub Pages](https://pages.github.com/) deployment via GitHub Actions (see [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml))

## Structure

| Path | Purpose |
|---|---|
| `content/blog/` | Thought leadership and industry analysis posts |
| `content/projects/` | Technical portfolio case studies |
| `static/resume.pdf` | Resume (linked from the homepage) |

Pushes to `main` trigger an automated build and deploy to the `gh-pages` branch.
