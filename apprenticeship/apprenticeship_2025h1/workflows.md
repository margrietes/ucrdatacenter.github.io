---
title: "Data Center Apprenticeship:\nEfficient workflows with GitHub and Quarto"
subtitle: "January 2025" 
date: "Last updated: 2024-12-24"
output:
  md_document:
    variant: gfm
    preserve_yaml: true
    toc: true
---

- [Introduction](#introduction)
- [Working with GitHub using GitHub
  Desktop](#working-with-github-using-github-desktop)
  - [Introduction to GitHub](#introduction-to-github)
  - [Forking a Repository](#forking-a-repository)
  - [Cloning a repository](#cloning-a-repository)
    - [Making Commits and Pushing
      Changes](#making-commits-and-pushing-changes)
    - [Creating a Pull Request](#creating-a-pull-request)
- [Part 2: Introduction to Markdown Documents with
  Quarto](#part-2-introduction-to-markdown-documents-with-quarto)
  - [What is Markdown?](#what-is-markdown)
  - [Creating a Quarto Document](#creating-a-quarto-document)
  - [Rendering the Document](#rendering-the-document)

# Introduction

In this tutorial, we will explore how to use GitHub and Quarto to
streamline your workflows, improve collaboration, and create
professional, reproducible documents.

This tutorial is divided into two parts:

1.  An introduction to using GitHub for version control and
    collaboration. GitHub is a version control platform that allows you
    to track changes, collaborate with others, and manage your projects
    efficiently. GitHub Desktop is a graphical user interface that
    simplifies working with Git repositories, making it more accessible
    to users.
2.  An overview of creating and using Markdown documents with Quarto for
    documentation and reporting. Quarto is a tool for creating documents
    using Markdown, enabling you to generate reports, presentations, and
    analyses with ease. (If you are familiar with RMarkdown, Quarto is a
    very similar tool with additional features.)

# Working with GitHub using GitHub Desktop

## Introduction to GitHub

GitHub is a web-based platform designed for version control and
collaboration. At its core, GitHub uses repositories (often abbreviated
as “repos”) to organize and store project files, including their full
revision history. A repository acts as a central storage location where
you can manage code, track changes, and collaborate with others.

You can create your own repositories, access any other public
repository, and contribute to open-source projects. You can also use
GitHub to track issues, manage projects, and host documentation. For
example, R packages are often hosted on GitHub, making it easy for users
to install and update packages directly from the platform, or view the
source code.

GitHub Desktop is a graphical user interface that simplifies working
with GitHub repositories. It eliminates the need to use the command
line, making Git more accessible. In this section, we’ll explore the
essential features of GitHub Desktop, including cloning and forking
repositories, committing changes, pushing updates, and creating pull
requests. These features help manage version control, ensure
reproducibility, and facilitate collaboration.

In the following, we guide you through an example workflow using GitHub
Desktop. This example is similar to the process you might follow if
contributing to the Data Center’s projects.

Before we begin, make sure that you have created a [GitHub
account](https://github.com/) and installed [GitHub
Desktop](https://desktop.github.com/download/) on your computer.

## Forking a Repository

Forking creates your own copy of someone else’s repository, allowing you
to make changes without affecting the original project. This is ideal
when contributing to open-source projects or experimenting with new
ideas. It is also a good way of making sure that the original repository
remains stable, as any changes to it need to be approved by the
repository owner.

When working with the DC’s repositories, you will typically fork the
repository to your own GitHub account, make changes in your fork, and
then create a pull request to propose changes to the original
repository. This method prevents version clashes if multiple
contributors are working on the same project.

Steps of forking a repository:

1.  Navigate to the repository page in your browser:
    `https://github.com/ucrdatacenter/projects`.
2.  Click the `Fork` button at the top-right corner of the page.
3.  Select your GitHub account as the destination for the fork.
4.  Clone your forked repository to your local machine using GitHub
    Desktop (refer to the steps for cloning above).

Forking is especially beneficial for collaboration. It ensures you can
test and develop features independently before proposing changes to the
main project.

``` r
# Placeholder for screenshot of forking process
```

------------------------------------------------------------------------

## Cloning a repository

Cloning a repository is useful when you want to work on an existing
project locally. It is the most convenient way of working with files, as
you can make changes while working normally in your local directory and
with any software installed on your computer.

The forked repo can be your own project or one you’re contributing to.
For example, if you’re working on a collaborative project like
`ucrdatacenter/projects`, cloning allows you to download the repository
to your local machine for modification.

Steps:

1.  Open GitHub Desktop.
2.  Go to `File > Clone Repository...`.
3.  Select the `URL` tab and either choose the repository from the list
    or enter the repository URL:
    `https://github.com/ucrdatacenter/projects`.
4.  Choose a local path where the repository will be saved and click
    `Clone`.

``` r
# Placeholder for screenshot of cloning process
```

------------------------------------------------------------------------

### Making Commits and Pushing Changes

Commits are snapshots of your changes that you save to the repository.
Pushing these changes updates the repository on GitHub, making them
available to collaborators.

Steps:

1.  Make changes to the files in your local repository.
2.  Open GitHub Desktop, where your changed files will be listed.
3.  Write a clear and descriptive commit message summarizing your
    changes (e.g., “Fixed bug in data processing script”).
4.  Click `Commit to main` to save the snapshot locally.
5.  Push your changes to GitHub by clicking the `Push origin` button.

Commits and pushes are essential for maintaining a history of changes
and sharing updates with collaborators. They ensure transparency and
accountability in project development. Note that a commit is a local
operation, i.e. it’s tracked locally, but not shared on GitHub or with
any of your collaborators until you push the changes.

``` r
# Placeholder for screenshot of commit and push process
```

------------------------------------------------------------------------

### Creating a Pull Request

A pull request is a way to propose changes to the original repository.
It allows the repository owner to review your modifications before
merging them. It is the method for moving the changes you made in your
fork back to the original repository.

Steps:

1.  Open your forked repository on GitHub.
2.  Click the `Pull requests` tab and then `New pull request`.
3.  Select the original repository as the base and your forked
    repository as the compare.
4.  Add a title and description for your pull request explaining the
    changes you’ve made.
5.  Click `Create pull request`.

Pull requests are valuable for collaborative projects, as they allow for
feedback and discussion before changes are finalized.

``` r
# Placeholder for screenshot of pull request creation
```

------------------------------------------------------------------------

# Part 2: Introduction to Markdown Documents with Quarto

## What is Markdown?

Markdown is a lightweight markup language used to create formatted text
with plain text editors. It’s widely used for documentation because of
its simplicity and readability. Quarto extends Markdown with additional
features to create professional documents, including reports,
presentations, and technical analyses.

If you are familiar with LaTeX, Markdown is a simpler alternative for
creating documents. It allows you to focus on content rather than
formatting, making it ideal for writing reports, notes, and
documentation.

------------------------------------------------------------------------

## Creating a Quarto Document

Quarto documents are ideal for reproducible research and professional
reporting. To create a Quarto document:

1.  Install Quarto from <https://quarto.org>.
2.  Open RStudio or another text editor.
3.  Create a new Quarto document via
    `File > New File > Quarto Document`.

A new `.qmd` file will be generated, containing the following structure:

``` markdown
---
title: "Document Title"
author: "Author Name"
date: "2024-12-24"
output: html_document
---

# Introduction
This is a sample Quarto document.


``` r
# Example of an R code chunk
summary(cars)
```

    ##      speed           dist       
    ##  Min.   : 4.0   Min.   :  2.00  
    ##  1st Qu.:12.0   1st Qu.: 26.00  
    ##  Median :15.0   Median : 36.00  
    ##  Mean   :15.4   Mean   : 42.98  
    ##  3rd Qu.:19.0   3rd Qu.: 56.00  
    ##  Max.   :25.0   Max.   :120.00


    This structure includes metadata (called the YAML header), plain text content, and code chunks for embedding analyses.

    ---

    ## Document Structure

    ### YAML Header
    The YAML header defines metadata for the document, such as the title, author, and output format. It’s placed at the top of the file and surrounded by triple dashes (`---`).

    ### Plain Text and Formatting

    Use plain text for content and Markdown syntax for formatting:

    - **Headings**: Use `#`, `##`, and `###` for different heading levels. Headings help structure your document.
    - **Lists**: Create lists to organize information clearly. Use `-` for unordered lists or numbers for ordered lists. You can create nested lists by indenting (4 spaces per level).
    - **Text Formatting**: Use `*italic*` or `**bold**` to emphasize text.

    ### Code Chunks

    Code chunks allow you to embed and execute code within the document. They are enclosed by triple backticks with a language specifier:

    ```markdown

    ``` r
    # Example of an R code chunk
    plot(mpg ~ wt, data = mtcars)

![](workflows_files/figure-gfm/unnamed-chunk-2-1.png)<!-- -->


    Code chunks are essential for integrating analysis directly into the document, ensuring reproducibility.

    ### Math Notation

    Use LaTeX syntax for mathematical expressions. For example:

    - Inline math: `$E = mc^2$`
    - Display math: `$$\int_0^1 x^2 \, dx = \frac{1}{3}$$`

    Math notation is especially useful in technical and scientific reports.

    ### Fenced Divs

    Fenced divs enable advanced formatting and layouts. For example:

    ```markdown
    :::: {.callout-note}
    ## Note
    This is a callout box.
    ::::

------------------------------------------------------------------------

## Rendering the Document

To render a Quarto document:

1.  Save the `.qmd` file.
2.  Click the `Render` button in RStudio.

Rendering produces the final output, such as HTML or PDF, making your
analysis and documentation accessible.
