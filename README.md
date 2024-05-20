# Creating and Using Python Packages with Robocorp

>[!NOTE]
>While every effort has been made to ensure the accuracy and reliability of the information provided in this documentation, it is possible that some content may contain errors or inaccuracies. We recommend cross-referencing with other sources and verifying the information before relying on it for critical purposes. If you identify any discrepancies or have suggestions for improvements, please open an issue.

This documentation provides a comprehensive guide on how to create and package your Python projects for use with Robocorp. Packaging your project enables other developers to easily install and integrate your work into their own projects. The Python Packaging Authority (PyPA) offers tools and resources to support the packaging and distribution of Python software.

>[!TIP]
>See [Further Resources](#further-resources) for more precise documentation on specific matters.

## Prerequisites

Before packaging your Python project, ensure you have:

1. Python 3.8 or later installed
2. The latest versions of `build` and `pip` installed.
3. A Python project with a `pyproject.toml` file.

### Installing Python

Download the latest Windows installer from [python.org](https://python.org), run it, and check "Add Python to PATH". Verify the installation by typing python --version in Command Prompt.

### Installing and upgrading `build` and `pip`

Install or upgrade `pip` by running:

```bash
py -m ensurepip --upgrade 
```

Install or upgrade `build` by running:

```bash
py -m pip install --upgrade build 
```

### pyproject.toml

The `pyproject.toml` file is increasingly used as it allows you to specify build system requirements separately. Here is an example:

```toml
[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
build-backend = "setuptools.build_meta"

[project]
name = "example_package_YOUR_USERNAME_HERE"
version = "0.0.1"
authors = [
  { name="Example Author", email="author@example.com" },
]
description = "A small example package"
readme = "README.md"
requires-python = ">=3.8"
classifiers = [
    "Programming Language :: Python :: 3",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
]

[project.urls]
Homepage = "https://github.com/pypa/sampleproject"
Issues = "https://github.com/pypa/sampleproject/issues"
```

## Project Structure

A typical Python project structure should include the following:

```text
my_project/
├── src/
│   └── my_package/
│       ├── __init__.py
│       ├── module1.py
│       └── module2.py
├── tests/
│   ├── __init__.py
│   ├── test_module1.py
│   └── test_module2.py
├── LICENSE
├── README.md
└── pyproject.toml
```

- `my_project`: Root directory of the whole project.
- `src/`: The directory where your packages code lives.
- `my_package`: Reprecents the Python package that contains your code
- `__init__.py`: Marks the directory as a Python package
- `module(x).py`: A module from your package
- `tests/`: Unit tests for your package code.`(optional)`
- `LICENSE`: License agreement for your project.
- `README.md`: Markdown file describing your project and how to use it.
- `pyproject.toml`: Configuration file for build system requirements.

## Building Your Package

To build your package, navigate to the directory containing `pyproject.toml` and run:

```bash
python -m build
```

This command will generate a source distribution (`tar.gz`) and a wheel (`whl`) in a `dist/` folder.

## Publishing Your Package

To upload your package to the Python Package Index (PyPI), you first need to install twine:

```bash
python -m pip install --user --upgrade twine
```

Then, upload your package to testpypi using:

```bash
python -m twine upload --repository testpypi dist/*
```

Make sure you're registered on [testPyPI](https://test.pypi.org/)

Then after a review of successful upload, upload your package to pypi using:

```bash
python -m twine upload --repository pypi dist/*
```

Make sure you're registered on [PyPI](https://pypi.org/)

## Versioning

Adhere to semantic versioning rules for your package. Update the `version` argument in `pyproject.toml` each time you publish a new release.

Semantic versioning is a standardized way of assigning version numbers to software releases in order to communicate the nature of changes in the codebase. It consists of three components: MAJOR.MINOR.PATCH.

- MAJOR: This could represent significant milestones or major releases where there are substantial changes or additions to the software's features, user interface, or underlying architecture. It indicates that there may be significant changes in functionality or user experience.

- MINOR: Minor version increments might signify smaller, but still notable, updates or enhancements that don't fundamentally alter the software's purpose or user experience. These could include the addition of new tools, improvements to existing features, or performance optimizations.

- PATCH: Patch releases would typically involve bug fixes or minor tweaks that address specific issues or improve stability without introducing new features or altering existing ones in a significant way.

>[!NOTE]
>For example, a version increment from 2.1.3 to 2.2.0 might indicate that significant new features have been added, while an increment from 2.1.3 to 2.1.4 might signal a minor bug fix or optimization.

## Continuous Integration

Consider setting up Continuous Integration (CI) to automatically build and publish your package into PyPI. If the project resides in a private GitHub repository, then you must consider the following limitations on GitHub action workflows:

|     Plan    | Storage | Minutes (per month) |
|:-----------:|:-------:|:-------------------:|
| GitHub Free | 500 MB  | 2,000               |

## Further Resources

- Python Packaging User Guide: <https://packaging.python.org>
- Sample project: <https://github.com/pypa/sampleproject>
- twine documentation: <https://twine.readthedocs.io/en/latest/>

The Python packaging ecosystem is evolving, and other methods may be worth exploring for possible simplified project configuration and dependency management features.

## Robocorp python packages

There are 3 different ways to have your python package added as a dependency into your robocorp task package.

![example of 3 different methods](resources/examples.jpg)

### 1.Installing from PyPI

To install a package from PyPI, specify it in your configuration file. If you do not specify a version, the latest version will be installed. However, note that without a locked version, it won't be cloud-cacheable.

> [!NOTE]
> Robocorp cloud caching can take up to 5-15 minutes to be available

---

> [!CAUTION]
> Must use Cloud Worker (early access) to enable caching! (as of 17.05.2024)

```yaml
- pip:
    - example_package==1.0.0      # https://pypi.org/project/example_package
```

>[!TIP]
>Adding a link to the [release notes](https://pypi.org/project/) of the packages helps maintenance and security.

### 2.Floating dependency from private github repository

For dependencies hosted on a GitHub repository, you can specify the repository URL in the pip section. This allows you to install the package directly from the source. You can pin to a specific branch, tag, or commit. If the repository is private, generate and use a [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) for access.

The line below specifies a package installation from a public GitHub repository:

```yaml
- pip:
    - git+https://github.com/example_author/example_package@V1.0.0
```

This is from a private GitHub repository:

```yaml
- pip:
    - git+https://example_author:github_pat_12345@github.com/example_author/example_package@V1.0.0
```

Let's break down each part:

1. **`git+https://`**
   - **Explanation**: This indicates that the package should be installed from a Git repository using the HTTPS protocol. The `git+` prefix tells the installer (pip) that this is a Git repository.

2. **`example_author:github_pat_12345`**
   - **`example_author`**: This is the GitHub username. It specifies the account on GitHub where the repository is hosted.
   - **`github_pat_12345`**: This is a placeholder for the personal access token (PAT). The PAT is used for authentication to allow access to private repositories. In practice, this should be a secure and confidential string, and never shared or hard-coded in public files.

3. **`@github.com/`**
   - **Explanation**: This specifies the host domain where the Git repository is located, which is GitHub in this case.

4. **`example_author/example_package`**
   - **Explanation**: This is the path to the repository on GitHub. It includes the username `example_author` and the repository name `example_package`.

5. **`@V1.0.0`**
   - **Explanation**: This specifies the version of the repository to install. In Git terms, this can be a branch, a tag, or a commit hash. Here, `V1.0.0` is a tag that points to a specific version of the code.

> [!WARNING]
> Never expose your personal access token in public repositories. Use environment variables or secure vaults to manage sensitive information.

---

> [!IMPORTANT]
> Use fine-grained personal access tokens for added security.  
> These tokens can only access specific repositories and have an expiration date.

### 3.Installing from a local folder

You can also install a package from a local folder by specifying its path. This is useful for development or testing when you have the package source code available locally and want to install it directly without uploading it to a package repository.

```yaml
-rccPostInstall:
    - pip install -e "C:\\Users\\example_author\\Documents\\example_package"
```

#### `-rccPostInstall:`

- **Explanation**: This section specifies post-installation commands for the Robocorp Control Center (RCC). Commands listed here will run after the main installation process.

#### `pip install -e`

- **Explanation**: The `pip install -e` command installs a package in "editable" mode. This means that any changes made to the source code will immediately affect the installed package without needing to reinstall it. This is particularly useful during development.

#### `"C:\\Users\\example_author\\Documents\\example_package"`

- **Explanation**: This is the path to the local folder containing the package. Note that the path should be enclosed in quotes and use double backslashes `\\` to escape the backslash character in Windows file paths.

---

## Action server example

Same 3 methods work in action-server aswell. Key difference is that package.yaml has a bit different structure.  
The 3 methods used are in `lines 18-22.`  
Below is an example how your package.yaml might look like.

![example of action server pacakge.yaml](resources/action-server-example.jpg)

>[!TIP]
>For additional info about syntax, see [action-server documentation](https://github.com/robocorp/robocorp/tree/master/action_server/docs)/guides/01-package-yaml.md.

---
