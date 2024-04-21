## Hosting Projects with ISYE

Many projects in our lab have online demos that we want permanently hosted. ISYE has a virtual machine (VM) set up to host these projects for us. The VM has a script that regularly pulls from GitHub repositories ("repos") under ISYE'S GT GitHub organization (https://github.gatech.edu/isye-web).

To host a project with ISYE, you need:

1. A server in your codebase. Most of our projects so far have used flask (Python) or JACO (Java). If you only need to host static HTML/CSS/JS files, ISYE has an Apache server that can do that.

2. A GitHub repo with ISYE for the project.

That means we need to sync our project's code to the corresponding repository 


How to set up GitHub Actions to sync with ISYE repos
