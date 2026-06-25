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

## Improving source code and its structure to make it more readable and understandable

* Organising and structuring your code and project directory keeps your software clean, modular, and reusable, enhancing its readability, extensibility, and reusability.
* Following coding conventions for your programming language produces consistently formatted code that others find it easy to read, reuse or extend in their own examples and applications.
* Writing structured documentation strings and comments within your code will make it more understandable to others who wish to use or extend it.

## Code correctness

* Testing can save time spent on debugging and ensures that your code is correct and does what it is set out to do, giving you and others confidence in your code and the results it produces.

### Making software easier for other people to reuse and repurpose

* Using virtual development environments ensures your software can be developed and run consistently across different systems, making it easier for you and others to run, reuse, and extend your code.
* Sharing code on collaboration platforms like GitHub or GitLab makes it easier to increase its visibility and work on it together.
* Using version control tools like Git makes it easier to track changes over time, while combining it with collaborative platforms like GitHub or GitLab makes it easier to work on the code as a team and in parallel.
* Collaborative code sharing platforms help promote openness and can help foster a community around your software, growing a user base and contributors for your software and contributing to its long-term sustainability.
* Providing clear and comprehensive documentation, including setup/installation guides, usage instructions and example use cases, reference manuals and API specifications for developers, etc. ensures your software is easy to understand, use, and extend (by you and others).
* Accompanying your software with clear information about its licensing terms and how it should be cited ensures that others can reuse and adapt your code with confidence and that you receive credit when they do so.
* Packaging code and publishing it in code repositories makes it easier for people to install and use your software.

### FAIR software

Some of the practices covered here also align with the FAIR Research Software Principles. 
However, FAIR is just one of several frameworks that can guide the development of high-quality research software. 
What matters most is recognising how each of these individual practices—whether or not they come from FAIR — helps you produce software that is more reliable, maintainable, and useful to others.