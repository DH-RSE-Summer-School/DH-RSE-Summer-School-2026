# Building Better Software to Support Open and Reproducible Research - Introduction

This course teaches good practices and reproducible working methods that are agnostic of a programming language (although we will use Python code in our examples). 
It aims to provide researchers with the tools and knowledge to feel confident when writing good quality and sustainable software to support their research. 
Although the discussion will often focus on software developed in the context of research, most of the good practices introduced here are beneficial to software development more generally.

## Reproducible Research

The lesson is particularly focused on one aspect of good (scientific) software development practice: improving software to enhance reproducibility. 
That is, enabling others to run our code and obtain the same results we did.

Reproducible research is a practice that ensures that researchers can repeat the same analysis multiple times with the same results. 
It offers many benefits to those who practice it:

* Reproducible research helps researchers remember how and why they performed specific tasks and analyses; this enables easier explanation of work to collaborators and reviewers.
* Reproducible research enables researchers to quickly modify analyses and figures - this is often required at all stages of research and automating this process saves loads of time.
* Reproducible research enables reusability of previously conducted tasks so that new projects that require the same or similar tasks become much easier and efficient by reusing or reconfiguring previous work.
* Reproducible research supports researchers’ career development by facilitating the reuse and citation of all research outputs - including both code and data.
* Reproducible research is a strong indicator of rigor, trustworthiness, and transparency in scientific research. This can increase the quality and speed of peer review, because reviewers can directly access the analytical process described in a manuscript. It increases the probability that errors are caught early on - by collaborators or during the peer-review process, helping alleviate the reproducibility crisis.

However, reproducible research often requires that researchers implement new practices and learn new tools. 
This course aims to teach some of these practices and tools pertaining to the use of software to conduct reproducible research.

## Practices for Building Better Research Software

We will cover some good software engineering practices that will help us build better research software.
This list is not exhaustive - you should keep exploring other practices too.
It can also be overwhelming to try and change all at once in your code - try doing some small changes first and make small improvements.
Small changes over time will compound together and lead to a much better software.
Next time you start a project from scratch - you will start using these practices from the beginning, saving yourself some time.

### Improving code structure to make it more readable and understandable

* Organising and structuring your code and project directory keeps your software clean, modular, and reusable, enhancing its readability, extensibility, and reusability.
* Following coding conventions for your programming language produces consistently formatted code that others find it easy to read, reuse or extend in their own examples and applications.
* Writing structured documentation strings and comments within your code will make it more understandable to others who wish to use or extend it.

### Improving code correctness and confidence in results it produces

* Testing can save time spent on debugging and ensures that your code is correct and does what it is set out to do, giving you and others confidence in your code and the results it produces.

### Making software easier for other people to reuse and repurpose

* Using virtual development environments ensures your software can be developed and run consistently across different systems, making it easier for you and others to run, reuse, and extend your code.
* Sharing code on collaboration platforms like GitHub or GitLab makes it easier to increase its visibility and work on it together.
* Using version control tools like Git makes it easier to track changes over time, while combining it with collaborative platforms like GitHub or GitLab makes it easier to work on the code as a team and in parallel.
* Collaborative code sharing platforms help promote openness and can help foster a community around your software, growing a user base and contributors for your software and contributing to its long-term sustainability.
* Providing clear and comprehensive documentation, including setup/installation guides, usage instructions and example use cases, reference manuals and API specifications for developers, etc. ensures your software is easy to understand, use, and extend (by you and others).
* Accompanying your software with clear information about its licensing terms and how it should be cited ensures that others can reuse and adapt your code with confidence and that you receive credit when they do so.
* Packaging code and publishing it in code repositories makes it easier for people to install and use your software.

## FAIR software

Some of the practices covered here also align with the [FAIR Research Software Principles](https://www.nature.com/articles/s41597-022-01710-x). 
FAIR stands for Findable, Accessible, Interoperable, and Reusable and comprises a set of principles designed to increase the visibility and usefulness of your research to others. 
The [FAIR data principles](https://www.nature.com/articles/sdata201618), first published in 2016, are widely known and applied today. 
Similar FAIR principles for software have now been defined too. In general, they mean:

* Findable - software and its associated metadata must be easy to discover by humans and machines.
* Accessible - in order to reuse software, the software and its metadata must be retrievable by standard protocols, free and legally usable.
* Interoperable - when interacting with other software it must be done by exchanging data and/or metadata through standardised protocols and application programming interfaces (APIs).
* Reusable - software should be usable (can be executed) and reusable (can be understood, modified, built upon, or incorporated into other software).

However, FAIR is just one of several frameworks that can guide the development of high-quality research software. 
What matters most is recognising how each of these individual practices — whether or not they come from FAIR — helps you produce software that is more reliable, maintainable, and useful to others.

## What we will do today?

### Exercise: Tools and practices you use

Individually, reflect on what practices or tools you are already using in your software development workflow, list some new practices or tools that you would like to start employing or using.
Write your reflections in the shared collaborative document.

### Scenario

You have inherited code from a post-doctoral researcher who has since left your group. 
The lead of your research group wants to publish a paper along with this code used to generate the analyses, with the hope that other researchers may apply the analysis to their own datasets and extend the capabilities of the project to other analyses. 

Your task is to download the code, understand what it does, run it on your machine reproducing its results, and improve the code's readability and structure using code reformatting and refactoring software engineering practices. 
Next, you will need to prepare the code for publication in a journal and reuse by others by applying good software engineering practices around software documentation, packaging and publishing, improving its maintainability and sustainability.

### Example code

The code you inherited from your colleague in located in GitHub - so it is already version controlled and shared in a more accessible way (which is good).

TODO - import from the example code repo (by using the template option) and open in GitHub's Codespaces.


### Better research software practices we will cover

We will go through a number of exercises that cover the following good practices:

* [Use reproducible software environments to make it easier to develop, install and run software](./applying-good-research-software-practices-exercises.md#reproducible-software-environments)
* Improving code structure to make it more readable and understandable
* Document and publish software for research publications, getting credit for our work and reuse by others

Some exercises we will do in "live-coding" style - the instructor will share their screen and do the exercise while everyone will follow along.
Other exercises you will do individually or in small groups.

