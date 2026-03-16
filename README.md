# Sphinx Gated Directives
<!-- Start content -->
This package is an extension for Sphinx that creates a start directive and an end directive for every registered class-based directive.

## What does it do?

This extension performs the following steps:
1. It scans the Sphinx environment for all registered class-based directives.
2. For each directive found, it automatically registers two new directives:
   - A **start** directive that marks the beginning of a block.
   - An **end** directive that marks the end of a block.
3. On build time, it processes these start and end directives to ensure that content between them is handled correctly.

For example, if you have a directive called `warning`, this extension will create (with _default settings_) two new directives: `warning-start` and `warning-end`. The code:

```markdown
:::{warning}
This is a warning message.

So, be careful!
:::
```

can now be replaced with the code

```markdown
:::{warning-start}
This is a warning message.
:::

So, be careful!

:::{warning-end}
:::
```

In short:
- This extension splits the original directive into a start and end directive.
- The start directive has the same options as the original directive.
- Anything inside the start directive is processed as usual.
- Anything between the start and end directives is processed as usual, and then added to the output of the start directive.
- The end directive is mandatory to close the directive.
- Anything inside the end directive is ignored.

This allows two things:
1. More granular control over where the directive starts and ends, which can be useful in complex documents. This is similar to how HTML tags and LaTeX environments work.
2. The ability to nest directives more easily, as the start and end markers can be placed independently. A major benefit of this is that it allows for nesting of code-cells.

<!-- Start caution -->

> [!CAUTION]
> Some directives parse the content inside the directive. If that content is moved between the start and end directives, it may not be parsed as expected, as it will be parsed separately.

<!-- End caution -->

## Directives with empty content

A final caveat must be told: some modules define directives that log Sphinx errors, but do not raise Exceptions, in case no content is found. For example, the module `docutils.parsers.rst.directives.admonitions` raises a Sphinx error with the message

```text
ERROR: Content block expected for the "admonition" directive; none found.
```

when the directive `admonition` is used without content. Other directives from those same modules often also log the same error. To avoid errors for the start directive, which may contain no content, this extension checks if the original directive is part of a module listed in the variable `NONEMPTY_CONTENT_MODULES` defined within [`__init__.py'](https://github.com/TeachBooks/Sphinx-Gated-Directives/blob/main/src/sphinx_gated_directives/__init__.py#L49) and if the content is empty, adds the dummy content `""` to the directive. This allows the start directive to be used without content, while still allowing the original directive to be used as usual.

Currently the modules listed in `NONEMPTY_CONTENT_MODULES` are:
- `docutils.parsers.rst.directives.admonitions`

If you as a user encounter an error for a directive that is not listed in `NONEMPTY_CONTENT_MODULES`, please report it to the developers, so that it can be added to the list. You can also create a pull request to add the module to the list yourself.

## Installation
To use this extension, follow these steps:

**Step 1: Install the Package**

Install the module `sphinx-gated-directives` package using `pip`:

```
pip install sphinx-gated-directives
```
    
**Step 2: Add to `requirements.txt`**

Make sure that the package is included in your project's `requirements.txt` to track the dependency:

```
sphinx-gated-directives
```

**Step 3: Enable in `_config.yml`**

In your `_config.yml` file, add the extension to the list of Sphinx extra extensions (**important**: underscore, not dash this time):

```yaml
sphinx: 
    extra_extensions:
        .
        .
        .
        - sphinx_gated_directives
        .
        .
        .
```

or similarly in your `conf.py` file.

## Configuration

This extension can be configured via the `_config.yml` file in your JupyterBook project (or similarly in `conf.py` for standard Sphinx projects).

The default configuration options are as follows:

```yaml
sphinx:
  config:
    sphinx_gated_directives:
      suffix_start: start    # Suffix for the start directive
      suffix_end: end        # Suffix for the end directive
      suffix_separator: '-'     # Separator between the original directive name and the suffix
      override_existing: false  # Whether to override existing gated directives with the same name
```

Per key the meanings are:
- `suffix_start`: The suffix to append to the original directive name for the start directive.
  - Default is `start`.
  - Must be a non-empty string containing only `a-z`.
- `suffix_end`: The suffix to append to the original directive name for the end directive.
  - Default is `end`.
  - Must be a non-empty string containing only `a-z`.
- `suffix_separator`: The separator to use between the original directive name and the suffix.
  - Default is `-`.
  - Must be a single character, no space, no underscore, no colon, or empty string.
- `override_existing`: Whether to override existing gated directives with the same name.
  - Default is `false`.
  - Must be a boolean value (`true` or `false`), a single string or a list of strings.
  - If `false`, none existing gated directives will be overridden.
  - If `true`, all existing gated directives will be overridden.
  - If a string or a list of strings, only the existing gated directives with names in the list/string will be overridden.

<!-- End configuration -->


> [!WARNING]
> Setting `override_existing` to anything other than `false` may lead to unexpected behavior if those directives are already in use.
> 
> Use with caution.

## Examples

Examples for this extension is available in the [TeachBooks manual](https://teachbooks.io/manual/_git/github.com_TeachBooks_Sphinx-Gated-Directives/main/MANUAL.html).

<!-- Start contribute -->
## Contribute

This tool's repository is stored on [GitHub](https://github.com/TeachBooks/Sphinx-Gated-Directives). If you'd like to contribute, you can create a fork and open a pull request on the [GitHub repository](https://github.com/TeachBooks/Sphinx-Gated-Directives).
