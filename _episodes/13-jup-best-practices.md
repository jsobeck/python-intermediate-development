---
title: "Best practices for Jupyter"
start: false
teaching: 15
exercises: 10
questions:
- "How to avoid chaos when writing code in Notebooks?"
- "What is the workflow when using Jupyter Lab for software development?"
objectives:
- "Follow best practice to ensure that your Notebook is well-organized and reusable."
keypoints:
- "The interactivity of Notebooks, while convenient, enables chaotic style of software development."
- "We must follow best practices for Jupyter Lab to avoid chaos in the Notebooks."
---

## Pros and Cons of Interactivity
Jupyter notebooks are very convenient since they allow executing pieces of code
in an arbitrary order, giving the developer a high level of interactivity. 
The other side of the coin is that notebooks enable the development of the code
without a clear structure, and that the result of the executed code differs depending
on which cells were executed prior to that. Taking care of keeping the code in order
falls on the developer's shoulders even more than when a classic IDE is used.

![Inattention to cell execution order can mess your data](../fig/13_jupBestPractice_1_cellOrder.png){: .image-with-shadow width="500px" }
<p style="text-align: center;">Inattention to cell execution order can mess your data</p>

As a result, Jupyter Lab is not recommended for large-scale software development, 
and even for smaller projects the final code should always be extracted into executable
'.py' files and converted into Python package. At the same time, notebooks are well-suited for 
data inverstigation, visualization and presentations, and it is the best when `.ipynb` files
contain only the code related to those tasks. Even then, without following certain 
best practices, notebooks have poor reproducibility. 

## Jupyter Lab best practices

Fortunately, Jupyter Lab provides us with a number of tools that allow us
to keep the notebook files clean, and the developed code reliable. Let's consider
the most important rules of keeping your notebooks in a good condition:

1. **Set the objective**. Define the objective of the notebook from the start and write it on the top of the notebook. Pay attention
   to how you phrase the objective: it should explain what is done in this notebook and be specific. E.g. instead of generic 
   'Some code for analysing the data' it is better to write 'Inspect, clean from NaNs and visualize on a histogram LSST RR Lyrae
   light curves dataset'.
2. **One notebook - one task**. One notebook should correspond to only one task or stage of your investigation.
   E.g. it is better to separate data preprocessing and visualization, or analysis of the spectra
   and analysis of the light curves. Do not stray from this objective; you definitely will get new ideas while working on your analysis,
   but if they are outside of the scope of this particular notebook, they should be extracted to a new file.
   There are two possible exceptions to this rule: if your notebook
   is really small (e.g. you just need to make a couple of plots), or if it's a 
   demonstration or presentation notebook.
3. **Structure first**.Think about the structure of the notebook before you start working, and write the headers of the sections
   in advance. For most astronomical projects, you will need at least four sections: imports, loading the data,
   pre-processing the data and analysis itself. Remember, that you can create subsections using secondary headers!
   It is also a good idea to put global variables, that later will be used across the code (e.g. sizes of samples, time ranges for
   period search, magnitude limits) into a separate section before the analysis, and create temporary sections for classes and functions
   (which ultimately should be extracted into `.py` files).
4. **Utilize Markdown cells** for detailed explanations of what is done in the following code cells. Markdown cells
   allow you to use headers, common types of text formatting, such as bold, italics and strikethrough formatting,
   create lists, separators and tables, insert latex equations and use HTML formatting and so on. Here is a 
   [cheat sheet](https://www.kaggle.com/code/cuecacuela/the-ultimate-markdown-cheat-sheet) for the 
   common types of formatting. To convert the cell into Markdown, you can press `Esc` and then `M`, or 
   by using the drop-down menu in the instrumental panel at the top of the notebook tab.
5. **Keep it short**. Keep your notebooks short. There is no hard rule, but constraining a notebook to a hundred of cells is 
   a good idea. If your notebook is longer than that, make sure that you follow the rule of 'One notebook - one task'.

> ## Jupyter Lab Table of Contents
> The benefit of using multi-level headers for sections and subsections is that Jupyter Lab uses them for creating
> the Table of Contents, which can be accesses from the collapsible left side-bar. With this panel, you can quickly evaluate the
> structure of your notebook, go to any subsection or execute all the cells under the selected header.
> ![Using the Table of Contents to execute cells in a selected section](../fig/13_jupBestPractice_2_ToC.png){: .image-with-shadow width="500px" }
> <p style="text-align: center;">Using the Table of Contents to execute cells in a selected section</p>
{: .callout}
> ## Fix the structure of the 'light-curve-analysis.ipynb' notebook
> Go through the best practices listed above one by one and improve the structure of the `light-curve-analysis.ipynb`
> notebook.
> > ## Solution
> > Let's go through the recommendations one by one.
> > 1. **Set the objective**. Right now the objective of the notebook is phrased in a generic way. We can rephrase it into, e.g.
> >    'Inspect the sizes and visualize light curves from the LSST and Kepler RR Lyrae datasets.'
> > 2. **One notebook - one task**. Since for now our notebook is small, we can leave it as it is. However, potentially we could
> >    have put visualization into a separate notebook.
> > 3. **Structure first**. Currently the structure of the notebook is not well-defined. Add headers for the sections dedicated to the
> >    inspection of the datasets and visialization of a light curve, put all imports into the corresponding section and move the global variables
> >    (in our case we can `plot_filter_labels`, `plot_filter_colors` and `plot_filter_symbols`) into a separate section. 
> > 4. **Keep it short**. Since our notebook has less than a hundred cells, for now we don't have this problem.
> > 5. **Utilize Markdown cells**. Give a brief description for each section (you can put it in the same cell as the headers).
> >    Use some formatting, e.g. in the 'Dataset inspection' section create a simple table listing the number of objects in current versions of
> >    each of the datasets.
> {: .solution}
{: .challenge}

6.If your Notebook contains pieces of code that are computationally
   extensive, work on a small representative sample of the data,
   and then put the code in an executable `.py` file to launch it from terminal. It will help you
   to avoid situations when the results are lost due to IDE crash, and also will make it possible
   to launch your analysis on the machines where Jupyter Lab is not available, e.g. on your institution servers.
   Regardless of whether you're launching such code from the terminal or from the Notebook, the result
   of this computation should be stored and then uploaded from disk. The
   cell with the code for this computation should have a boolean flag that will determine whether it
   should execute. It is not recommended to use commenting instead, since it does not inform the user
   of the condition when the code has to be executed. 
7. Use 'Restart and Run All' often, and always before finishing your work, to make sure that
   your results are reproducible.
9. Convert the code you use more than once into functions.
10. Classes and functions have to be taken out of the Notebooks and put in `.py` files. This allows you
   to use them again across multiple Notebooks and then package them to release as a standalone tool.

> ## Jupyter Notebook, Rubin Science Platform and Google Colab
> While RSP and Google Colab have Jupyter Notebook installed and not Jupyter Lab, all of the
> best practices above are still applicable on these platworms, with the only exception that
> you will have to create the Table of Contents manually.
{: .callout}

> ## Exercises
> Fix `light-curve-analysis.ipynb` structure
{: .challenge}

{% include links.md %}
