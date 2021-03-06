[![Actions Status](https://github.com/timvink/mkdocs-git-authors-plugin/workflows/pytest/badge.svg)](https://github.com/timvink/mkdocs-git-authors-plugin/actions)
![PyPI - Python Version](https://img.shields.io/pypi/pyversions/mkdocs-git-authors-plugin)
![PyPI](https://img.shields.io/pypi/v/mkdocs-git-authors-plugin)
![PyPI - Downloads](https://img.shields.io/pypi/dm/mkdocs-git-authors-plugin)
[![codecov](https://codecov.io/gh/timvink/mkdocs-git-authors-plugin/branch/master/graph/badge.svg)](https://codecov.io/gh/timvink/mkdocs-git-authors-plugin)
![GitHub contributors](https://img.shields.io/github/contributors/timvink/mkdocs-git-authors-plugin)
![PyPI - License](https://img.shields.io/pypi/l/mkdocs-git-authors-plugin)

# mkdocs-git-authors-plugin

[MkDocs](https://www.mkdocs.org/) plugin to display git authors of a markdown page:

> Authors: Jane Doe, John Doe

The plugin only considers authors of the current lines in the page ('surviving code' using `git blame`).

Other MkDocs plugins that use information from git:

- [mkdocs-git-committers-plugin](https://github.com/byrnereese/mkdocs-git-committers-plugin) for displaying authors' github user profiles
- [mkdocs-git-revision-date-localized-plugin](https://github.com/timvink/mkdocs-git-revision-date-localized-plugin) for displaying the last revision date

## Setup

Install the plugin using pip3:

```bash
pip3 install mkdocs-git-authors-plugin
```

Next, add the following lines to your `mkdocs.yml`:

```yml
plugins:
  - search
  - git-authors
```

> If you have no `plugins` entry in your config file yet, you'll likely also want to add the `search` plugin. MkDocs enables it by default if there is no `plugins` entry set.

## Usage

### In markdown pages

You can use ``{{ git_authors_summary }}`` to insert a summary of the authors of a page. Authors are sorted by their name and have a `mailto:` link with their email.

An example output:

```html
<span class='git-authors'><a href='mailto:jane@abc.com'>Jane Doe</a><a href='mailto:john@abc.com'>John Doe</a></span>
```

Which renders as:

> [Jane Doe](mailto:#), [John Doe](mailto:#)

### In supported themes

no supported themes *yet*.

### In theme customization

[MkDocs](https://www.mkdocs.org/) offers possibilities to [customize an existing theme](https://www.mkdocs.org/user-guide/styling-your-docs/#customizing-a-theme).

As an example, if you use [mkdocs-material](https://github.com/squidfunk/mkdocs-material) you can implement git-authors by [overriding a template block](https://squidfunk.github.io/mkdocs-material/customization/#overriding-template-blocks):

1) Create a new file `base.html` in `docs/assets/theme`:

```html
{% extends "base.html" %}

{% block disqus %}
    <div class="md-source-date">
    <small>
        Authors: {{ git_authors_summary }}
    </small>
  </div>
    {% include "partials/integrations/disqus.html" %}
{% endblock %}
```

2) In `mkdocs.yml` make sure to specify the custom directory with the theme overrides:

```yml
theme:
    name: material
    custom_dir: docs/assets/theme/
```

### In theme templates

To add more detailed author information to your theme you can [customize a MkDocs theme](https://www.mkdocs.org/user-guide/styling-your-docs/#customizing-a-theme) or even [develop your own](https://www.mkdocs.org/user-guide/custom-themes/). When enabling this plugin, you will have access to the jinja2 variable `git_authors`, which contains a list of authors dicts, like the following example:

```python
[{
    'name' : 'Jane Doe',
    'email' : 'jane@abc.com',
    'last_datetime' : datetime.datetime(),
    'lines' : 200,
    'contribution' : '40.0%'
},
{
    'name' : 'John Doe',
    'email' : 'john@abc.com',
    'last_datetime' : datetime.datetime(),
    'lines' : 300,
    'contribution' : '60.0%'
}]
```

#### Example usage in theme development

An example of how to use in your templates:

```django hljs
{% if git_authors %}
  {%- for author in git_authors -%}
    <a href="{{ author.email }}" title="{{ author.name }}">
      {{ author.name }}
    </a>,
  {%- endfor -%}
{% endif %}
```

Alternatively, you could use the simple preformatted ``{{ git_authors_summary }}`` to insert a summary of the authors.

## Options

### `show_contribution`

If this option is set to `true` (default: `false`) The contribution to a page is
printed as a percentage of (source file) lines per author. The output is
suppressed if there is only *one* author for a page.

Example output:

* Authors: [John Doe](#) (33.33%), [Jane Doe](#) (66.67%) *(more than one author)*
* Authors: [John Doe](#) *(one author)*

### Aggregating Authors

In some repositories authors may have committed with differing name/email combinations.
In order to prevent the output being split it is possible to aggregate authors on
arbitrary elements by providing a file `.mailmap` in the repository's root directory.
This is a feature of Git itself. The following example will aggregate the contributions
of Jane Doe committed under two email addresses:

```
# .mailmap
Jane Doe <jane.doe@company.com> <jane.doe@private-email.com>
```

This will map commits made with the `private-email.com` to the company address. For more details
and further options (e.g. mapping between different names or misspellings etc. see the
[git-blame documentation](https://git-scm.com/docs/git-blame#_mapping_authors).

## Contributing

Very much open to contributions! Please read [CONTRIBUTING.md](CONTRIBUTING.md) before putting in any work.
