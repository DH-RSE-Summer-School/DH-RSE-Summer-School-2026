# Building Better Software to Support Open and Reproducible Research - Exercises

<details>
<summary><b>Table of Contents</b></summary>

- **Table of Contents**
  - [Scenario](#scenario)
  - [Starter code](#starter-code)
  - [**A. Obtain and inspect the software project**](#a-obtain-and-inspect-the-software-project)
    - [**A**.1 *Essential task:* Make a copy the software project](#a1-essential-task-make-a-copy-the-software-project)
    - [**A.2** *Essential task:* Open and expect software project in a code editor of choice](#a2-essential-task-open-and-expect-software-project-in-a-code-editor-of-choice)
  - [**B. Reproducible software environments**](#b-reproducible-software-environments)
    - [**B.1** *Essential task:* Create a virtual development environment using `venv`](#b1-essential-task-create-a-virtual-development-environment-using-venv)
    - [**B.2** *Essential task:* Install your software's dependencies into virtual development environment using `pip`](#b2-essential-task-install-your-softwares-dependencies-into-virtual-development-environment-using-pip)
    - [**B.3** *Essential task:* Create `requirements.txt` file to record dependencies](#b3-essential-task-create-requirementstxt-file-to-record-dependencies)
  - [**1. Code formatting \& structure for readability**](#1-code-formatting--structure-for-readability)
    - [**1.1** *Essential task:* Place import statements at the top](#11-essential-task-place-import-statements-at-the-top)
    - [**1.2** *Essential task:* Improve code structure \& formatting](#12-essential-task-improve-code-structure--formatting)
    - [**1.3** *Essential task:* Improve variable naming](#13-essential-task-improve-variable-naming)
    - [**1.4** *Essential task:* Remove unused variables](#14-essential-task-remove-unused-variables)
    - [**1.5** *Essential task:* Refactor the script into functions and use standard libraries](#15-essential-task-refactor-the-script-into-functions-and-use-standard-libraries)
    - [*Essential task:* Use `main()` function (Can be removed?)](#essential-task-use-main-function-can-be-removed)
    - [**1.6** *Optional task:* Add input command-line arguments to allow for a flexible input dataset](#16-optional-task-add-input-command-line-arguments-to-allow-for-a-flexible-input-dataset)
    - [**1.7** *Optional task:* Add input command-line arguments to allow for a flexible location to save results](#17-optional-task-add-input-command-line-arguments-to-allow-for-a-flexible-location-to-save-results)
  - [**2. Software documentation**](#2-software-documentation)
    - [**2.1** *Essential task:* Add descriptive comments to code](#21-essential-task-add-descriptive-comments-to-code)
    - [**2.2** *Essential task:* Add docstrings to functions](#22-essential-task-add-docstrings-to-functions)
    - [**2.3** *Essential task:* Add a README file](#23-essential-task-add-a-readme-file)
    - [**2.4** *Optional task:* Go through a software quality checklist](#24-optional-task-go-through-a-software-quality-checklist)
  - [**3. Publishing software**](#3-publishing-software)
    - [**3.1** *Essential task:* Create a DOI](#31-essential-task-create-a-doi)
    - [**3.2** *Essential task:* Add a `LICENSE` file](#32-essential-task-add-a-license-file)
    - [**3.3** *Essential task:* Add a copyright statement](#33-essential-task-add-a-copyright-statement)
    - [**3.4** *Essential task:* Add a `CITATION.cff` file](#34-essential-task-add-a-citationcff-file)
    - [**3.5** *Essential task:* Release software on GitHub + Zenodo](#35-essential-task-release-software-on-github--zenodo)
    - [**3.6** *Optional task:* Package the software project](#36-optional-task-package-the-software-project)
    - [**3.7** *Optional task:* Prepare the work for publication in the Journal of Open Source Software](#37-optional-task-prepare-the-work-for-publication-in-the-journal-of-open-source-software)
  - [Tasks: Formatting \& Refactoring (45 min)](#tasks-formatting--refactoring-45-min)
    - [*Essential:*  Improve formatting](#essential--improve-formatting)
    - [*Essential:*  Fix non-DRY code](#essential--fix-non-dry-code)
    - 
</details>

## Scenario

You have inherited code from a post-doctoral researcher who has since left your group.
The lead of your research group wants to publish a paper along with this code used to generate the analyses, with the hope that other researchers may apply the analysis to their own datasets and extend the capabilities of the project to other analyses.

Your task is to download the code, understand what it does, run it on your machine reproducing its results, and improve the code's readability and structure using code reformatting and refactoring software engineering practices.
Next, you will need to prepare the code for publication in a journal and reuse by others by applying good software engineering practices around software documentation, packaging and publishing, improving its maintainability and sustainability.

## Starter code

The code you inherited from your colleague in located in a [software project repository in GitHub](https://github.com/softwaresaved/DH-RSE-Summer-School-2026-Day3-code/) - so it is already version controlled and shared in a more accessible way (which is good).

The software project contains Python code that uses the NASA data on human space walks (Extravehicular activities - EVAs) undertaken by astronauts and cosmonauts from 1965 to 2013 (data provided by NASA via its Open Data Portal).
The code does some analysis over this data.

In detail, the project contains:

- JSON file called `eva_data.json` with data on extra-vehicular activities (EVAs, i.e. spacewalks).
- Python script `eva_data_analysis.py` that does some common research tasks:
  - Reads in the data from the JSON file
  - Changes the data from one data format to another and saves to a file in the new format (CSV)
  - Performs some calculations to generate summary statistics about the data
  - Makes a plot to visualise the data

This project is intentionally constructed to illustrate some common mistakes in research software development.
Throughout the session, you will learn and apply better research software practices — including elements of FAIR — as you work to improve the software project.

## **A. Obtain and inspect the software project**

### **A**.1 *Essential task:* Make a copy the software project

- **Description:** Make a copy of the software project into your GitHub space so you can continue working on it.
- **Task:**
  - Log in to GitHub.
  - Go to <https://github.com/softwaresaved/DH-RSE-Summer-School-2026-Day3-code/>.
  - Click `Use this template` button to create a copy of the template code repository in your own GitHub.

### **A.2** *Essential task:* Open and expect software project in a code editor of choice

- **Description:**
- **Task:**
  - Using Git from command line, checkout locally your copy of the software project from GitHub and open in, for example, VS Code or another code editor of your choice.
  - Alternatively, open the software project in GitHub's Codespace.

## **B. Reproducible software environments**

**Virtual development environments** help us create an **isolated working copy** of a software project that uses a specific version of Python interpreter together with specific versions of a number of external libraries (that our software depends on) installed into that virtual environment.
Python virtual environments are implemented as directories with a particular structure within software projects, containing links to specified dependencies allowing isolation from other software projects on your machine that may require different versions of Python or external libraries.

Virtual environments are not just a feature of Python - most modern programming languages use a similar mechanism to isolate libraries or dependencies for a specific project, making it easier to develop, run, test and share code with others.

It is recommended to create a separate virtual environment for each software project.
Then you do not have to worry about changes to the environment of the current project you are working on affecting other projects - you can use different Python versions and different versions of the same third party dependency by different projects on your machine independently of one another.

### **B.1** *Essential task:* Create a virtual development environment using `venv`

- **Description:**  `venv` command-line tool (and a Python module) is a package installer and manager for Python (as of Python v3.3 it is included as part of a standard Python distribution).
The venv module supports creating lightweight “virtual environments”, each with their own independent set of Python packages installed in their site directories.
- **Task:** Create and activate a Python virtual environment using `venv`.
- **More information:** : <https://docs.python.org/3/library/venv.html>

Creating a virtual environment called **".venv"** with the `venv` command line tool is done by executing the following command from the project root:

```python
$ python3 -m venv .venv
$ source .venv/bin/activate # Linux and macOS
$ source .venv/Scripts/activate # Windows
```

>[!NOTE]
> On some systems you may have to invoke the Python and Pip commands as `python` and `pip`, respectively.

The above commands will create a folder **".venv"** within the root of your project, where information about your virtual environment will be located.

You should see your terminal's prompt change now to include the name if the virtual environment in round braces - `(.venv)` - to indicate that the environment is active.
You could have called your virtual environment something else - by convention they are called **"venv"** or **".venv"**, with the caveat that it may cause confusion when you have multiple environments all called **"venv"**.

When you are done working on your project, you can exit/deactivate the environment with:

```python
(.venv) $ deactivate
```

### **B.2** *Essential task:* Install your software's dependencies into virtual development environment using `pip`

- **Description:**  `pip` command-line tool (and a Python module) is a package installer and manager for Python (as of Python v3.3 it is included as part of a standard Python distribution). You can use it to install packages from the Python Package Index and other indexes.
- **Task:** Identify dependencies for your software and install them into an active virtual environment using `pip`.
- **More information:** : <https://pip.pypa.io/en/stable/>

You can install your software's dependencies into your active environment using `pip` as follows:

```python
(.venv) $ pip3 install matplotlib pandas
```

You can see all packages currently installed in your environment with:

```python
(.venv) $ pip3 list
```

### **B.3** *Essential task:* Create `requirements.txt` file to record dependencies

- **Description:** The `requirements.txt` file can be used to list the packages (and their versions) that the project depends on for proper execution and makes installation of these dependencies easy using `pip` command-line tool. A `requirements.txt` file reduces the likelihood of compatibility issues and ensures that a project is well-documented, maintainable, and reproducible.
- **Task:** Create a `requirements.txt` file in the root directory of the project. Populate this file with a list of Python packages that the program relies on using `pip` and make sure that you include only packages that are actually used by your software.
- **More information:** : <https://www.geeksforgeeks.org/how-to-create-requirements-txt-file-in-python/>

To export your active virtual development environment that contains your software's dependencies you can use `pip freeze` command.
It will produce a list of packages installed in your virtual development environment.
A common convention is to save this list in a `requirements.txt` file in your project’s root directory:

```python
(.venv) $ pip3 freeze > requirements.txt
```

You should put `requirements.txt` under version control and share it along with our code - so that others can more easily reproduce the same environment, should they wish to run or modify your code.

```python
(.venv) $ git add requirements.txt
(.venv) $ git commit -m "Initial commit of requirements.txt"
(.venv) $ git push origin main
```

To recreate a virtual environment from `requirements.txt` (e.g. on another machine), from the project root one should create the virtual environment and then install dependencies from the requirements file into that environment:

```python
$ python3 -m venv .venv
$ source venv/bin/activate
(.venv) $ pip install -r requirements.txt
```

## **1. Code formatting & structure for readability**

### **1.1** *Essential task:* Place import statements at the top

- **Description:** Conventionally, all import statements are placed at the top of the script so that dependent libraries are clearly visible and not buried inside the code.
This helps with readability and reusability of our code.
- **Task:** Modify `eva_data_analysis.py` script so that all import statements are placed at the top of the file.

### **1.2** *Essential task:* Improve code structure & formatting

- **Description:** Code can become considerably more readable with the addition of blank lines that group lines of code into logical sections, and by following a consistent style guide such as PEP 8 (e.g. import statements grouped at the top of the file, consistent spacing around operators, lines kept to a reasonable length).
- **Task:** Open `eva_data_analysis.py`. Notice the script runs as one long, flat sequence of statements at module level (no `main()`, no blank-line separation between logical sections such as "read data", "summarise by astronaut", and "plot"). Reformat the script so that related statements are visually grouped with blank lines, and check it against PEP 8 (<https://peps.python.org/pep-0008/>). You may find it helpful to run a formatter/linter such as `black` or `flake8` over the file.
- **More information:** : <https://peps.python.org/pep-0008/>

### **1.3** *Essential task:* Improve variable naming

- **Description:** Variable and function names should succinctly indicate what a function does or a variable means. When variable and function names are uninformative, code can be considerably harder to understand. Single-letter or cryptic names (`f`, `o`, `d`, `g`, `h`, `m`, `hrs`, `hrs2`) force a reader to trace back through the code to figure out what's being stored. As a rule of thumb, the length of the name should be proportional to the scope and complexity of the variable or function, and formatting conventions (such as using snake_case or camelCase) should be consistent throughout the project.
- **Task:** Locate the lines marked `TODO Naming` in `eva_data_analysis.py` and rename the flagged variables (e.g. `f` → something describing the input file, `d` → something describing the cleaned EVA dataframe, `o` and `g` → something describing the output CSV/graph paths, `hrs`/`hrs2` → something describing duration in hours) to be clear and descriptive.
There are additional unmarked variables in the script (e.g. `h`, `m`, `val`) that could also be improved - don't limit yourself to only the marked lines.
- **More information:** : The Python style guide (PEP 8: <https://peps.python.org/pep-0008/>) provides rules for consistent formatting, including use of blank space, naming conventions, and comments, and is generally followed by production-level software projects.

### **1.4** *Essential task:* Remove unused variables

- **Description:** Dead code - variables or functions that are defined but never used - adds confusion for future readers, who may assume it serves some purpose or waste time trying to find where it is being used or called.
- **Task:** The function `calculate_crew_size` is defined near the bottom of `eva_data_analysis.py` (marked with a `TODO`) but is never called anywhere in the script.
Decide whether to remove it, or to actually use it by adding a `crew_size` column to the dataset - either is a reasonable choice, but document your decision in a comment.

### **1.5** *Essential task:* Refactor the script into functions and use standard libraries

- **Description:** Each function should accomplish one logical task, enabling the script to read like a series of instructions, rather than as one long unbroken block of statements.
- **Task:** `eva_data_analysis.py` currently has no functions at all - everything happens at module level.
Identify the distinct pieces of functionality in the script (reading the JSON file, writing a dataframe to CSV, converting a duration string to hours, summarising duration by astronaut, plotting the cumulative time graph) and factor each into its own function. Then add a `main()` function that calls them in sequence, and a `if __name__ == "__main__":` block that calls `main()`.
- **More information:** : <https://realpython.com/python-main-function/>

### *Essential task:* Use `main()` function (Can be removed?)

### **1.6** *Optional task:* Add input command-line arguments to allow for a flexible input dataset

- **Description:** Executable scripts allow for flexible processing and code reuse through the use of input arguments. By changing the script to accept input arguments, the analysis could be easily applied to other collections of files.
- **Task:** Locate the lines indicated by “TODO Inputs” and change the script to accept different inputs, such as a single file, a list of file locations, or a directory containing multiple files. All lines indicated by the comment “TODO Inputs” are related to the use of input arguments, although not all of them will need to be changed. The input should include a complete path to the location of the input arguments or be able to create a complete path from the input arguments.
- **More information:** : <https://www.geeksforgeeks.org/command-line-arguments-in-python/>
- **Task Dependencies:** This task relies on the following tasks to be completed prior to beginning this task:
  - Formatting and Refactoring: Translate the notebook into an executable python script

### **1.7** *Optional task:* Add input command-line arguments to allow for a flexible location to save results

- **Task:** As in the preceding task, change the main script to accept a second input argument. This second input argument should be a string that indicates the location where the output histogram figure will be saved, including the complete path to that location. Change the code that saves the histogram figure to use the updated location.
- **Task Dependencies:** This task relies on the following tasks to be completed prior to beginning this task:
  - Formatting and Refactoring: Translate the notebook into an executable python script

## **2. Software documentation**

### **2.1** *Essential task:* Add descriptive comments to code

- **Description:** Comments should be useful and informative to future developers of the project. They can explain the overall outline of the code, describe specific intent of certain sections of the code, and explain specific algorithmic decisions. In Python, comments begin with a hash (#) symbol on each line of the comment.
- **Task:** Comments are provided throughout the project, but there are instances where comments are missing (indicated by the placeholder comment “Descriptive comment”), the comments are not sufficiently descriptive, or the formatting of comments is inconsistent. Step through the notebook and add or edit comments throughout to explain specific lines and blocks.
- **More information:** : <https://realpython.com/python-comments-guide/>

### **2.2** *Essential task:* Add docstrings to functions

- **Description:** In Python, the initial comment in a function or script that describes the objectives and interface is referred to as a docstring. The docstring describes the purpose, parameters, and return values of the function or script. Python includes a built-in function help() that prints the docstring for the input to help() to the console, so docstrings should ideally contain all information that will help guide a user in using the function or script. Docstrings are denoted by three quotation marks (""") before and after the docstring and can span multiple lines.
- **Task:** Include a docstring at the beginning of the main script and at the beginning of each function. The docstrings should describe the objective, interface (the expected inputs and outputs), and specific implementation.
- **More information:** : For more guidance on how to write docstrings and examples of docstrings, see this tutorial: <https://www.dataquest.io/blog/documenting-in-python-with-docstrings/>

### **2.3** *Essential task:* Add a README file

- **Description:** A README file describes the purpose and components of a software project and provides potential users with instructions on how to install and run the software. The file will also list the current contributors to the project, how others can contribute to the project, and where to find relevant resources. On GitHub, the README file also acts as the landing page for the repository project and will be the first thing that any visitors to the repository will see.
- **Task:** Edit the provided README.md file for the project to describe how the components of the project fit together. Include stepwise instructions on downloading and running the project and how to test the project output using the provided test data file `test_data.txt` located in the `data` directory. Also include a message encouraging others to contribute to the project and outlining how contributions can be made. Use the following template to organise the contents of the README: <https://ha0ye.github.io/CW21-README-tips/template_README.html>
- **More information:** : <https://book.the-turing-way.org/project-design/pd-overview/project-repo/project-repo-readme/>

### **2.4** *Optional task:* Go through a software quality checklist

- **Description:** Software quality checklists can help you write good quality software and align software quality standards across software projects. They also help others who are viewing or contributing to a project understand the state of the code and what could still be improved. A software quality checklist is an assessment of the current state of the code, rather than a list of tasks that should be completed before reporting the results of the checklist.
- **Task:** Go through the following software quality checklist and evaluate the current state of the software project: <https://fairsoftwarechecklist.net/v0.2/>. When you are finished, include the checklist as part of the README, in its own section.
- **More information:** : Other software quality checklists and an explanation for their use can be found here: <https://fair-software.nl/recommendations/checklist>
- **Task Dependencies:** This task relies on the following tasks to be completed prior to beginning this task:
  - Documentation: Add a README file

## **3. Publishing software**

### **3.1** *Essential task:* Create a DOI

- **Description:** A digital object identifier (DOI) is a unique and persistent identifier that enables proper attribution and reproduction. Zenodo is a data archiving tool that is commonly used to create DOIs for digital research objects.
- **Task:** In Zenodo (<https://zenodo.org/>), log in or create an account via the menu in the top right corner. Then, go to “new upload” and add details about the project. Click the “reserve” button to get the DOI. Include this DOI in the project README and in any other relevant documents such as the CITATION.cff file (created in the below task, Publishing: Add a CITATION.cff file).  Download the repository from GitHub as a compressed .zip file and upload the compressed repository to Zenodo. Add details of all contributors to the project in the Zenodo entry and include a link to the GitHub repository.
- **More information:** : To learn more about depositing records on Zenodo, visit the records documentation page here: <https://help.zenodo.org/docs/deposit/about-records/>; Zenodo is also directly integrated with GitHub and allows you to mint a DOI for public repositories which you own. A tutorial for minting DOIs directly for GitHub repositories can be found here: <https://docs.github.com/en/repositories/archiving-a-github-repository/referencing-and-citing-content>

### **3.2** *Essential task:* Add a `LICENSE` file

- **Description:** A software licence describes how a piece of software can legally be used. The licence is a legal agreement between the software developer(s) and the users. By default, any creative work (such as code) is under exclusive copyright, so the authors of open-source code must explicitly grant permission for others to use their work through a licence. Depending on the needs of your project, there are many open-source licenses that may be appropriate. You can find guidelines on choosing a licence here: <https://choosealicense.com/>, and the Open Source Initiative (OSI) also maintains a list of open-source and accredit licences here: <https://opensource.org/licenses>  
- **Task:** Select a license file appropriate for the given project and add it as a plain text file named LICENSE.txt in the top-most (root) project directory.
- **More information:** : For more information on licensing, we recommend this guide provided by the Turing Institute: <https://book.the-turing-way.org/reproducible-research/licensing>
<https://www.data.cam.ac.uk/data-management-guide/choosing-software-licence>

### **3.3** *Essential task:* Add a copyright statement

- **Description:** A copyright statement indicates who owns the intellectual property included in the research code. It is important to establish who owns the intellectual property and therefore who can licence the software. All contributors to the project are considered copyright holders but sometimes, if the contributors are not students and the work was completed using time or resources provided by an employer, the contributor’s employer may hold the copyright. This differs from institution to institution.
- **Task:** Include a copyright statement at the beginning of your licence file, stating the copyright holders (in this case, yourself and the fictional post doc).
- **More information:** : The Legal Side of Open Source <https://opensource.guide/legal/>

### **3.4** *Essential task:* Add a `CITATION.cff` file

- **Description:** Adding a citation file provides clear information on how to cite your work and ensures authors receive credit for their software development work while improving dissemination and software sustainability. The citation file format (cff) provides citation metadata for software in a human- and machine-readable format.
- **Task:** Include a CITATION.cff file in the top-most (root) directory of the project repository, using the example_citation.cff file in the repository as a template.
- **More information:** : <https://citation-file-format.github.io/>, <https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-citation-files>
- **Task Dependencies:** This task relies on the following tasks to be completed prior to beginning this task:
  - Publishing: Create a DOI
  
### **3.5** *Essential task:* Release software on GitHub + Zenodo

- **Description:** Once a software project has reached a milestone in its development, either in the development of new features or integration of new packages, a “release” of the package is created
- **Task:** Create an initial release of your project, following these instructions: <https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository>
- **More information:** : <https://docs.github.com/en/repositories/releasing-projects-on-github>

### **3.6** *Optional task:* Package the software project

- **Description:** Packaging a python project enables others to easily access it using the Python Package Index (PyPI) by using the command “pip install mypackage” where mypackage is the name of the python project. Packaging your Python projects enables others to easily implement your analyses, validating your findings and extending them to other datasets.
- **Task:** Package your python project by following this tutorial: <https://packaging.python.org/en/latest/tutorials/packaging-projects/>

### **3.7** *Optional task:* Prepare the work for publication in the Journal of Open Source Software

- **Description:** The Journal of Open Source Software (JOSS) is an open access journal for research software packages. JOSS enables the quality of software to be improved through a formal peer review process while giving researchers a citable DOI from an academic journal.
- **Task:** The JOSS review criteria (<https://joss.readthedocs.io/en/latest/review_criteria.html>) contain several of the recommended tasks already completed in this workshop. To prepare a submission for JOSS, you must prepare a short paper and a metadata file. See the JOSS guidelines for submission (<https://joss.readthedocs.io/en/latest/submitting.html>) for more guidance.

---
Old tasks

## Tasks: Formatting & Refactoring (45 min)

### *Essential:*  Improve formatting

- **Description:** Code can become considerably more readable with the addition of blank lines that group lines of code into logical sections. Although badly formatted code will still run, it is very difficult for others to read and interpret and thus limits the likelihood that others will use and extend the code.
- **Task:** First, review the code to determine the main functionality that is implemented. Then, use blank lines to group lines of code with common functionality and visually separate sections of the code that perform different tasks.

### *Essential:*  Fix non-DRY code

- **Description:** DRY stands for Don’t Repeat Yourself. DRY code is streamlined to remove code repetitions, for instance when multiple lines could be better implemented in a single line by efficiently using existing function calls, by using a loop, or by creating a new function, and considerably improves readability and clarity.
- **Task:** Locate the lines indicated by “TODO DRY” and modify these sections to remove repetition by taking advantage of existing code, adding in a function call, or using a loop, as appropriate.
