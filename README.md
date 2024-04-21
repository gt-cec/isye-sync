## Hosting Projects with ISYE

Many projects in our lab have online demos that we want permanently hosted. ISYE has a virtual machine (VM) set up to host these projects for us. The VM has a script that regularly pulls from GitHub repositories ("repos") under ISYE'S GT GitHub organization (https://github.gatech.edu/isye-web).

To host a project with ISYE, you need:

1. Your project code on a repo on one of the CEC GitHubs (github.com/gt-cec or github.gatech.edu/cec).

2. A server in your codebase. If you only need to host static HTML/CSS/JS files, ISYE has an Apache server that can do that.

3. A GitHub repo with ISYE for the project. 

4. A subdomain that ISYE will link to your project in the VM.

5. A GitHub Action to automatically sync your code on the CEC GitHub (github.com/gt-cec) with the repo on the ISYE GitHub (github.gatech.edu/isye-web). 

This document details how to do steps 2-5. It is assumed that you have been using Git and GitHub for your project.

As of April 21, 2024, here is the status of the ISYE-hosted projects:



Most of our projects so far have used flask (Python) or JACO (Java).

If your project does not already have an ISYE repo, email `helpdesk@isye.gatech.edu` and ask for a repo on the isye-web organization.

That means we need to sync our project's code to the corresponding repository 


How to set up GitHub Actions to sync with ISYE repos
