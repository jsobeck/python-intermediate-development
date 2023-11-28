---
title: "Integrated Software Development Environments"
start: false
teaching: 25
exercises: 15
questions:
- "What are Integrated Development Environments (IDEs)?"
- "What are the advantages of using IDEs for software development?"
- "How does Jupyter Lab interact with virtual environments?"
objectives:
- "Set up Jupyter Lab and its kernels"
- "Use Jupyter Lab to run a script"

keypoints:
- "An IDE is an application that provides a comprehensive set of facilities for software development, including
syntax highlighting, code search and completion, version control, testing and debugging."
- "Jupyter Lab launches within the pre-existing virtual environment"
- "We can run terminal commands within Jupyter Lab"
---

## Introduction
As we have seen in the previous episode -
even a simple software project is typically split into smaller functional units and modules,
which are kept in separate files and subdirectories.
As your code starts to grow and becomes more complex,
it will involve many different files and various external libraries.
You will need an application to help you manage all the complexities of,
and provide you with some useful (visual) facilities for,
the software development process.
Such clever and useful graphical software development applications are called
Integrated Development Environments (IDEs).

## Integrated Development Environments
An IDE normally consists of at least a source code editor,
build automation tools
and a debugger.
The boundaries between modern IDEs and other aspects of the broader software development process
are often blurred.
Nowadays IDEs also offer version control support,
tools to construct graphical user interfaces (GUI)
and web browser integration for web app development,
source code inspection for dependencies and many other useful functionalities.
The following is a list of the most commonly seen IDE features:

- syntax highlighting -
  to show the language constructs, keywords and the syntax errors
  with visually distinct colours and font effects
- code completion -
  to speed up programming by offering a set of possible (syntactically correct) code options
- code search -
  finding package, class, function and variable declarations, their usages and referencing
- version control support -
  to interact with source code repositories
- debugging -
  for setting breakpoints in the code editor,
  step-by-step execution of code and inspection of variables

IDEs are extremely useful and modern software development would be very hard without them.
There are a number of IDEs available for Python development;
a good overview is available from the
[Python Project Wiki](https://wiki.python.org/moin/IntegratedDevelopmentEnvironments).
In addition to IDEs, there are also a number of code editors that have Python support.
Code editors can be as simple as a text editor
with syntax highlighting and code formatting capabilities
(e.g., GNU EMACS, Vi/Vim).
Most good code editors can also execute code and control a debugger,
and some can also interact with a version control system.
Compared to an IDE, a good dedicated code editor is usually smaller and quicker,
but often less feature-rich.
You will have to decide which one is the best for you -
in this course, we will use [Jupyter Lab](https://jupyter.org/install) - a free open-source web-based IDE 
familiar to most Python-coding astronomers.

> ## (PyCharm OR VS Code OR Spyder) vs. (Jupyter Notebooks OR Jupyter lab)
> Explanation of the differences between full-scale IDEs and Jupyter IDEs
{: .callout}

> ## Jupyter Notebooks vs. Jupyter lab
> Explanation of the difference between Jupyter Notebooks and Jupyter Lab
{: .callout}

## Using the Jupyter Lab

Let's open our project in Jupyter Lab now and familiarise ourselves with some commonly used features.

### Managing Jupyter Kernels

### Opening a Software Project

> ## Exercise: Check where external packages are installed in Jupyter Lab 
> Check that packages installed in the Notebook end up in the right environment
>
> Hint: We can use an argument to `pip`,
> or find the packages directly in a subdirectory of our virtual environment directory "venv".
>
>> ## Solution
>> From the previous episode,
>> you may remember that we can get the list of packages in the current virtual environment
>> using the `pip3 list` command:
>> ~~~
>> (venv) $ pip3 list
>> ~~~
>> {: .language-bash}
>> ~~~
>> Package         Version
>> --------------- -------
>> cycler          0.11.0
>> fonttools       4.28.1
>> kiwisolver      1.3.2
>> matplotlib      3.5.0
>> numpy           1.21.4
>> packaging       21.2
>> Pillow          8.4.0
>> pip             21.1.3
>> pyparsing       2.4.7
>> python-dateutil 2.8.2
>> setuptools      57.0.0
>> setuptools-scm  6.3.2
>> six             1.16.0
>> tomli           1.2.2
>> ~~~
>> {: .output}
>> However, `pip3 list` shows all the packages in the virtual environment -
>> if we want to see only the list of packages that we installed,
>> we can use the `pip3 freeze` command instead:
>> ~~~
>> (venv) $ pip3 freeze
>> ~~~
>> {: .language-bash}
>> ~~~
>> cycler==0.11.0
>> fonttools==4.28.1
>> kiwisolver==1.3.2
>> matplotlib==3.5.0
>> numpy==1.21.4
>> packaging==21.2
>> Pillow==8.4.0
>> pyparsing==2.4.7
>> python-dateutil==2.8.2
>> setuptools-scm==6.3.2
>> six==1.16.0
>> tomli==1.2.2
>> ~~~
>> {: .output}
>> We see `pip` in `pip3 list` but not in `pip3 freeze` as we did not install it using `pip`.
>> Remember that we use `pip3 freeze` to update our `requirements.txt` file,
>> to keep a list of the packages our virtual environment includes.
>> Python will not do this automatically;
>> we have to manually update the file when our requirements change using:
>> ~~~
>> pip3 freeze > requirements.txt
>> ~~~
>> {: .language-bash}
>>
>> If we want, we can also see the list of packages directly in the following subdirectory of `venv`:
>> ~~~
>> (venv) $ ls -l venv/lib/python3.9/site-packages
>> ~~~
>> {: .language-bash}
>>
>> ~~~
>> total 1088
>> drwxr-xr-x  103 alex  staff    3296 17 Nov 11:55 PIL
>> drwxr-xr-x    9 alex  staff     288 17 Nov 11:55 Pillow-8.4.0.dist-info
>> drwxr-xr-x    6 alex  staff     192 17 Nov 11:55 __pycache__
>> drwxr-xr-x    5 alex  staff     160 17 Nov 11:53 _distutils_hack
>> drwxr-xr-x    8 alex  staff     256 17 Nov 11:55 cycler-0.11.0.dist-info
>> -rw-r--r--    1 alex  staff   14519 17 Nov 11:55 cycler.py
>> drwxr-xr-x   14 alex  staff     448 17 Nov 11:55 dateutil
>> -rw-r--r--    1 alex  staff     152 17 Nov 11:53 distutils-precedence.pth
>> drwxr-xr-x   31 alex  staff     992 17 Nov 11:55 fontTools
>> drwxr-xr-x    9 alex  staff     288 17 Nov 11:55 fonttools-4.28.1.dist-info
>> drwxr-xr-x    8 alex  staff     256 17 Nov 11:55 kiwisolver-1.3.2.dist-info
>> -rwxr-xr-x    1 alex  staff  216968 17 Nov 11:55 kiwisolver.cpython-39-darwin.so
>> drwxr-xr-x   92 alex  staff    2944 17 Nov 11:55 matplotlib
>> -rw-r--r--    1 alex  staff     569 17 Nov 11:55 matplotlib-3.5.0-py3.9-nspkg.pth
>> drwxr-xr-x   20 alex  staff     640 17 Nov 11:55 matplotlib-3.5.0.dist-info
>> drwxr-xr-x    7 alex  staff     224 17 Nov 11:55 mpl_toolkits
>> drwxr-xr-x   39 alex  staff    1248 17 Nov 11:55 numpy
>> drwxr-xr-x   11 alex  staff     352 17 Nov 11:55 numpy-1.21.4.dist-info
>> drwxr-xr-x   15 alex  staff     480 17 Nov 11:55 packaging
>> drwxr-xr-x   10 alex  staff     320 17 Nov 11:55 packaging-21.2.dist-info
>> drwxr-xr-x    8 alex  staff     256 17 Nov 11:53 pip
>> drwxr-xr-x   10 alex  staff     320 17 Nov 11:53 pip-21.1.3.dist-info
>> drwxr-xr-x    7 alex  staff     224 17 Nov 11:53 pkg_resources
>> -rw-r--r--    1 alex  staff      90 17 Nov 11:55 pylab.py
>> drwxr-xr-x    8 alex  staff     256 17 Nov 11:55 pyparsing-2.4.7.dist-info
>> -rw-r--r--    1 alex  staff  273365 17 Nov 11:55 pyparsing.py
>> drwxr-xr-x    9 alex  staff     288 17 Nov 11:55 python_dateutil-2.8.2.dist-info
>> drwxr-xr-x   41 alex  staff    1312 17 Nov 11:53 setuptools
>> drwxr-xr-x   11 alex  staff     352 17 Nov 11:53 setuptools-57.0.0.dist-info
>> drwxr-xr-x   19 alex  staff     608 17 Nov 11:55 setuptools_scm
>> drwxr-xr-x   10 alex  staff     320 17 Nov 11:55 setuptools_scm-6.3.2.dist-info
>> drwxr-xr-x    8 alex  staff     256 17 Nov 11:55 six-1.16.0.dist-info
>> -rw-r--r--    1 alex  staff   34549 17 Nov 11:55 six.py
>> drwxr-xr-x    8 alex  staff     256 17 Nov 11:55 tomli
>> drwxr-xr-x    7 alex  staff     224 17 Nov 11:55 tomli-1.2.2.dist-info
>> ~~~
>> {: .output}
>>
>> Finally, if you look at both the contents of
>> `venv/lib/python3.9/site-packages` and `requirements.txt`
>> and compare that with the packages shown in PyCharm's Python Interpreter Configuration -
>> you will see that they all contain equivalent information.
> {: .solution}
{: .challenge}


> ## Exercise: Update `requirements.txt` After Adding a New Dependency
> Export the newly updated virtual environment into `requirements.txt` file.
>> ## Solution
>> Let's verify first that the newly installed library `pytest` is appearing in our virtual environment
>> but not in `requirements.txt`. First, let's check the list of installed packages:
>> ~~~
>> (venv) $ pip3 list
>> ~~~
>> {: .language-bash}
>> ~~~
>> Package         Version
>> --------------- -------
>> attrs           21.4.0
>> cycler          0.11.0
>> fonttools       4.28.5
>> iniconfig       1.1.1
>> kiwisolver      1.3.2
>> matplotlib      3.5.1
>> numpy           1.22.0
>> packaging       21.3
>> Pillow          9.0.0
>> pip             20.0.2
>> pluggy          1.0.0
>> py              1.11.0
>> pyparsing       3.0.7
>> pytest          6.2.5
>> python-dateutil 2.8.2
>> setuptools      44.0.0
>> six             1.16.0
>> toml            0.10.2
>> tomli           2.0.0
>> ~~~
>> {: .output}
>> We can see the `pytest` library appearing in the listing above. However, if we do:
>> ~~~
>> (venv) $ cat requirements.txt
>> ~~~
>> {: .language-bash}
>> ~~~
>> cycler==0.11.0
>> fonttools==4.28.1
>> kiwisolver==1.3.2
>> matplotlib==3.5.0
>> numpy==1.21.4
>> packaging==21.2
>> Pillow==8.4.0
>> pyparsing==2.4.7
>> python-dateutil==2.8.2
>> setuptools-scm==6.3.2
>> six==1.16.0
>> tomli==1.2.2
>> ~~~
>> {: .output}
>> `pytest` is missing from `requirements.txt`. To add it, we need to update the file by repeating the command:
>> ~~~
>> (venv) $ pip3 freeze > requirements.txt
>> ~~~
>> {: .language-bash}
>> `pytest` is now present in `requirements.txt`:
>> ~~~
>> attrs==21.2.0
>> cycler==0.11.0
>> fonttools==4.28.1
>> iniconfig==1.1.1
>> kiwisolver==1.3.2
>> matplotlib==3.5.0
>> numpy==1.21.4
>> packaging==21.2
>> Pillow==8.4.0
>> pluggy==1.0.0
>> py==1.11.0
>> pyparsing==2.4.7
>> pytest==6.2.5
>> python-dateutil==2.8.2
>> setuptools-scm==6.3.2
>> six==1.16.0
>> toml==0.10.2
>> tomli==1.2.2
>> ~~~
> {: .solution}
{: .challenge}


### Syntax Highlighting
The first thing you may notice is that code is displayed using different colours.
Syntax highlighting is a feature that displays source code terms
in different colours and fonts according to the syntax category the highlighted term belongs to.
It also makes syntax errors visually distinct.
Highlighting does not affect the meaning of the code itself -
it's intended only for humans to make reading code and finding errors easier.

![Syntax Highlighting Functionality in Jupyter Lab](../fig/imgDummy.png){: .image-with-shadow width="800px" }

### Code Completion
As you start typing code,
PyCharm will offer to complete some of the code for you in the form of an auto completion popup.
This is a context-aware code completion feature
that speeds up the process of coding
(e.g. reducing typos and other common mistakes)
by offering available variable names,
functions from available packages,
parameters of functions,
hints related to syntax errors,
etc.

![Code Completion Functionality in Jupyter Lab](../fig/imgDummy.png){: .image-with-shadow width="600px" }

### Code Definition & Documentation References
Jupyter Lab tools: Tab, Shift+Tab, Ctrl+I
![Code References Functionality in Jupyter Lab](../fig/imgDummy.png){: .image-with-shadow width="800px" }

### Code Search
You can search for a text string within a project,
use different scopes to narrow your search process,
use regular expressions for complex searches,
include/exclude certain files from your search, find usages and occurrences.
To find a search string in the whole project:


### Version Control
Version control in Jupyter Lab: Git extension. **Maybe it's worth moving this part to another day, so that 
people didn't have to install extensions mid-episode (it may take time and cause problems...)**

### Running Scripts in Jupyter Lab


{% include links.md %}
