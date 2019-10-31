# Development Procedure for JDAT Notebooks

This document is a description of the JWST Data Analysis Tools Approach to
"Notebook-Driven Development".  The procedures here outline the process for
getting a notebook through sucessive development stages to become something
that can be "live" on the spacetelescope notebooks repository.

These notebooks can have many varied science cases, but follow a relatively
standard workflow:

1. Notebook Concept
2. Notebook Draft
3. Integrated Notebook
4. Final/Public Notebook
5. Revised based on Community feedback
6. (Repeat 5 into infinity)

These stages and the process for moving from one to the other are described below.  

Note that there is much more information on writing Jupyter notebooks at the [STScI notebook style guide](https://github.com/spacetelescope/style-guides/blob/master/guides/jupyter-notebooks.md), and similar guideance for Python code at the [STScI Python style guide](https://github.com/spacetelescope/style-guides/blob/master/guides/python.md).  These guidelines are in place to make review steps easier 

## Notebook Concept

The primary purpose of this stage is to record a scientific workflow, but without in actual code. This stage is generally done primarily by a scientist. Reasonably often, notebooks can skip this stage if they are simpler or if the underlying tools are already well-enough developed to be immediately implemented.

To begin a notebook at this stage, the notebook author should start with either the notebook template from the [notebook style guide](https://github.com/spacetelescope/style-guides/blob/master/guides/jupyter-notebooks.md) or a blank Jupyter notebook.  They then write out their workflow in words.  Where possible, they put  *example* code of the sort they would *like* to see, even if it's not implemented yet.  For example,  an author might write this in such a notebook:
```
In [ ]: spectral_line = find_line(jwst_miri_spectrum)

`spectral_line` should be a list of line centers and names of lines indexed by spaxel, found using a derivative-based line-finder.  
``` 
even if the ``find_line`` function doesn't yet exist anywhere.

The top-level header of the notebook (i.e., the title) should have "Concept: " at the start to make it clear this is a concept notebook.  The filename should *not* have `concept` in it, however, as the filename will generally remain the same throughout the later stages.

Once they have the concept ready, the author should create a pull request with the concept notebook's content:


### New Notebook Pull Request

Submission of a new notebook follows the git Pull Request workflow.  This is not described in detail here because more details are in the [STScI git workflow style guide](https://github.com/spacetelescope/style-guides/blob/master/guides/git-workflow.md).  But don't hesitate to reach out for help from other members of the team if you are stuck or aren't sure how it's supposed to work! 

To submit a concept notebook, the author should first fork and clone the notebooks working space repository: https://github.com/spacetelescope/dat_pyinthesky . Then for a add the new notebook in a sub-directory of the ``jdat_notebooks`` directory with the name of the notebook (see the repo itself for examples).  Then create the pull request using the name of the notebook as the PR title (including "Draft:"/"Content:"/etc as described below).  IMPORTANT: for a concept notebook, also be sure to add the name of the notebook to the ``exclude_notebooks`` file, to prevent the tests from running on the notebook (since it isn't intended to be functional yet anyway).  Once you've added the notebook to git, push it up to your fork and create a Pull Request (see the procedures document linked above for more detail).

One of the team members can then merge your Pull Request.  For concept notebook, nothing more than a cursory review (ensuring just that the notebook is readable and perhaps asking for clarification in a few areas) is necessary for merging.


## Notebook Draft

The primary purpose of this stage is to get a functioning notebook to record a workflow. This stage is also typically done by a scientist (although with developers available to ask questions).  It is also frequently the *first* step of development.  That is, if the workflow is already reasonable to implement with existing tools, the concept notebook is not necessary.

In this stage the notebook should actually execute from beginning to end, but it is fine to be "rough around the edges".  E.g., the notebook might have several cells that say things like:
```
...
In [ ]: spec = Spectrum(np.linspace(a, b, 1000)*u.angstrom, some_complex_function(...))

Creating the spectrum above is a bit complicated, and it would improve the workflow if there was a single simple function that just did ``spec = simulate_jwst_spectrum(a, b)``
```
thereby providing guidance for where specific development would simplify the workflow.

If a notebook is freshly created in this form, the author can follow the "New Notebook Pull Request" procedure above in the "Content Notebook" section. The only difference is that they should *not* add the notebook to ``exclude_notebooks``, as the notebook should be run once it's in draft stage.  

If the notebook was already created in the Concept Notebook stage, for this step, the author should follow the [standard git/github forking workflow](https://github.com/spacetelescope/style-guides/blob/master/guides/git-workflow.md) for modifying existing code. I.e., create a branch on their github fork, and then create a Pull Request with the changes once they are ready.

In either case, the title (but not filename) of the notebook should begin with "Draft:" to indicate the notebook is in the draft stage.

One the Pull Request has been created, the notebook will automatically be built in the repository so that reviewers can view it.  Reviewers can then comment on the notebook in Github.  At the draft stage the bar is still relatively low for review - primarily things like ensuring the notebook does run from beginning-to-end and that data files or the like were not accidentally committed to the repository.

Finally, there are three important technicalities for notebooks that become relevant at this stage (and continue for future stages):

1. The output cells of a notebook should *always* be cleared before a git commit is made. Notebook outputs can sometimes be quite large (in the megabytes for plots or the like), and git is intended for source code, not data. Clearing the outputs also ensures the notebook can be run from beginning to end and therefore be reproduced by others.
2. Any data files required for a notebook need to be accessible by others who may be reviewing or testing the notebook.  The [STScI guidelines on data storage for notebooks](https://github.com/spacetelescope/style-guides/blob/master/guides/where-to-put-your-data.md) have more detail here.  Note that if a draft notebook is using data that should not yet be public, the easiest choice is probably central store, but in that case it is critical that the notebook state prominently that it must be run inside the STScI network.
3. A notebook should state clearly what version of various dependencies were used to generate the notebook.  These versions should be placed in a `requirements` file in the same directory as the notebook itself.  That will ensure reviewers/testers can be sure that if they encounter problems, it's not due to software version mis-matches.


## Concept/Draft notebook-driven development

Between the concept and draft, or draft and polished stages, there is potential for considerable development to be necessary.  A draft notebook may contain a large number of areas where more development is desired in data analysis tools, or it may only require a few minor adjustments (or none at all!).  This stage is therefore the most flexible and dependent on developer resources, etc.  In general the intent is for developers to be able to re-use bits of code from the notebook as tests for development, while occasionally (if necessary) asking the notebook author for guidance to ensure the implementation actually meets the notebook's needs.  There is not a formal process for this step, but it is intended that the JDAT planning process (currently on JIRA) keeps track of specific steps needed before a given notebook can proceed on to the next stage.

## Integrated Notebook

Once a draft notebook has been completed, the next stage is to build the draft into a notebook that uses the DAT's or associated community-developed software as consistently as possible.  This is typically done via a developer reviewing a draft notebook and working with the scientist to use DAT software where relevant, or developing additional DAT code when necessary (see the above section).  It is at the discretion of the notebook author and developer together which of them actually modifies the notebook and sources the Pull Request, but it is likely both will be involved to some degree. The default approach is for the developer to take the draft notebook, mark it up with comments like (using the example from above):
```

...
In [ ]: spec = Spectrum(np.linspace(a, b, 1000)*u.angstrom, some_complex_function(...))

Creating the spectrum above is a bit complicated, and it would improve the workflow if there was a single simple function that just did ``spec = simulate_jwst_spectrum(a, b)``

EJT: This has now been implemented as JWSTSimulator.make_spectrum(a, b, anotherparameterthatturnsouttobeimportant).  Can you try that and ensure it works here?
```
and then create a git commit with these comments.  The original author would then address the comments in a follow-on commit, with implementation of all comments then being the step that allows both to declare the notebook ready to be called "Integrated".

Once the notebook authors (original author and developer/reviewer) have agreed it is ready, one of them follows the Pull Request workflow as described above, but with the notebook title now changed to be just the title itself (no "Concept:" or Draft:"). The Pull Request is then reviewed by one of the project scientists, and merged when there everyone is satisfied with the notebook.

## Final/Public Notebook

The final stage for the notebook is release on the [official STScI notebook repository](https://github.com/spacetelescope/notebooks). Specific documentation for this last stage is given in the repository itself.  However, that repository and the working repository here have very similar structure, so it is in principle simply a matter of copying the Integrated Notebook over to a form of the release repository and doing one final Pull Request.  Note, however, that other STScI reviewers may comment on this stage.  It is also important for the authors to do an additional check over the notebook to ensure that it uses *released* (not developer) versions of requirements where possible. It is also a good opportunity to fill in the scientific context of a given notebook - e.g. add a motivation section, or a final plot at the bottom that shows the final science result.  Once this is done, and the Pull Request merged, the Notebook can be declared complete.

## Community-driven Improvements

Of course, science does not stand still!  As time passes some of the completed notebooks may have enhancements or changes necessary.  In general these follow the standard Pull Request workflow and can be submitted by anyone once the notebook is public (both in and out of STScI).  While the repo maintainers manage this process, the notebook authors may be called in from time to time to provide opinions or perspectives on any proposed changes.
