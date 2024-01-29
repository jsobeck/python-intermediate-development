---
title: "Best practices for Jupyter"
start: false
teaching: 15
exercises: 10
questions:
- "How to avoid chaos when writing code in Notebooks?"
- "What is the workflow when using Jupyter Lab for software development?"
objectives:
- "Apply 'best practice' rules to ensure that your Notebook is well-organized and reusable."
keypoints:
- "The interactivity of Notebooks, while convenient, enables chaotic style of software development."
- "We must follow best practices for Jupyter Lab to avoid chaos in the Notebooks."
---

## Jupyter Lab best practices
Jupyter Notebooks are very convenient since they allow executing pieces of code
in an arbitrary order, giving the developer a high level of interactivity. 
The other side of the coin is that Notebooks enable the development of the code
without a clear structure, and that the result of code execution differs depending
on which cells were executed prior to that. Without following certain best practices,
Notebooks have poor reproducibility.

Fortunately, Jupyter Lab provides us with a number of tools that allow us
to keep the Notebook files clean, and the developed code reliable. Let's consider
the most important rules of keeping your Notebooks in a good condition:

1. One Notebook - one task. It is better to separate e.g. data preprocessing and visualization.
2. Define the objective of the Notebook from the start.
3. Use Table of Content. Start each section of the Notebook with a header in a markdown cell.
4. The structure of the Notebook should group functionally similar cells together.
   It means that package imports should all be in the beginning of the Notebook, the functions
   in a separate section next, the data upload and preprocessing next, the parameters next. Avoid situations
   when some of the parameters that are used in several places across your Notebook (e.g. the size of the sample
   or time range for searching variability period) are defined out of the parameter section.
6. Use Markdown cells for detailed explanations of what is done in the following code cells.
7. Use 'Restart and Run All' often, and always before finishing your work, to make sure that
   your results are reproducible.
8. If your Notebook contains pieces of code that are computationally
   extensive, it is better to work on a small representative sample of the data,
   and then put the code in an executable `.py` file to launch it from terminal. It will help you
   to avoid situations when the results are lost due to IDE crash, and also will make it possible
   to launch your analysis on the machines where Jupyter Lab is not available, e.g. on your institution servers.
   Regardless of whether you're launching such code from the terminal or from the Notebook, the result
   of this computation should be stored and then uploaded from disk. The
   cell with the code for this computation should have a boolean flag that will determine whether it
   should execute. It is not recommended to use commenting instead, since it does not inform the user
   of the condition when the code has to be executed. 
9. Convert the code you use more than once into functions.
10. Classes and functions have to be taken out of the Notebooks and put in `.py` files. This allows you
   to use them again across multiple Notebooks and then package them to release as a standalone tool.

> ## Exercises
> Fix `light-curve-analysis.ipynb` structure
{: .challenge}

{% include links.md %}
