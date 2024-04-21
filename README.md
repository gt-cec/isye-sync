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

| Project | Status | Domain | CEC Repo | ISYE Repo | Type | People |
| :-- | :-: | :-: | :-: | :-: | :-: | :-: |
| ASMM | - | asmm.cec.gatech.edu | github.com/gt-cec/asmm | github.gatech.edu/isye-web/cec-asmm | JATOS (Java) | Kolb, Srivastava |
| MAISR | - | maisr.cec.gatech.edu | github.com/gt-cec/maisr | github.gatech.edu/isye-web/cec-maisr | Flask (Python) | Agbeyibor, Kolb |
| ONR-ISR | - | onr-isr.cec.gatech.edu | github.com/gt-cec/onr-isr | github.gatech.edu/isye-web/cec-onr-isr | Flask (Python) | Agbeyibor, Kolb |
| TMM-HAI | In progress | tmm-hai.cec.gatech.edu | github.com/gt-cec/tmm-hai | github.gatech.edu/isye-web/cec-tmm-hai | Flask (Python) | Kolb |
| TMM-HRI | - | tmm-hri.cec.gatech.edu | github.com/gt-cec/tmm-hri | github.gatech.edu/isye-web/cec-tmm-hri | - | Kolb |
| TMM-MAS | - | tmm-mas.cec.gatech.edu | github.com/gt-cec/tmm-mas | github.gatech.edu/isye-web/cec-tmm-mas | - | Alag |

### Using a webserver for your project

Most of our projects use flask (Python) or JATOS (Java). Our projects typically require two sets of dependencies: system dependencies (`apt-get install XXX`) and language-specific libraries (`pip install XXX`).

ISYE's VM runs Red Hat Enterprise Linux (RHEL). RHEL is Debian-based, like Ubuntu, so for most purposes they are identical. The most significant difference is that Ubuntu's package manager is called `apt`, while RHEL's is called `yum`. Most packages on Ubuntu have an equivalent on RHEL.

Some libraries advertise support for CentOS, which is an open source version of RHEL.

For our projects to run on the VM, we need to include three files at the top level of our project.

1. `yum-requirements.txt` is a simple list of `yum` packages to install, for example:

```
pytorch
gdb
jatos
```

If a package is not available on yum, 

2. `pip-requirements.txt` is a simple list of `pip` packages to install, for example:

```
matplotlib
opencv
flask
pygame
numpy==1.0.2
```

Note how I specified a NumPy version. You can follow that format for any version-specific packages.

If your project does not already have an ISYE repo, email `helpdesk@isye.gatech.edu` and ask for a repo on the isye-web organization.

That means we need to sync our project's code to the corresponding repository 


How to set up GitHub Actions to sync with ISYE repos
