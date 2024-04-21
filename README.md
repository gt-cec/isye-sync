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

### Setting up your project for deployment

The goal of this section is to set up your codebase so ISYE knows what external packages to install and so all the VM has to do is run a bash script to start your project webserver.

**The first thing to do is remove all API keys, usernames/passwords, and user data from your codebase.** API keys, username, and passwords should always be [environment variables](https://www.freecodecamp.org/news/how-to-set-an-environment-variable-in-linux/). For IRB purposes, user data should never be on GitHub, which you can ensure by [making a `.gitignore` file that excludes the log files](https://www.w3schools.com/git/git_ignore.asp?remote=github). If you have already added sensitive info to GitHub, you can [squash your repository](https://stackoverflow.com/a/9254257) so people cannot go through the commit history and retrieve it.

Once your codebase and GitHub repository are clean from things that could get you an angry email from the IRB, you can proceed.

Most of our projects use flask (Python) or JATOS (Java). Our projects typically require two sets of dependencies: system dependencies (`apt-get install XXX`) and language-specific libraries (`pip install XXX`).

ISYE's VM runs Red Hat Enterprise Linux (RHEL). RHEL is Debian-based, like Ubuntu, so for most purposes they are identical. Ubuntu's package manager is called `apt`, while RHEL's is called `yum`.

Most `apt` packages have an equivalent on `yum`, or have a downloadable `.rpm` package that can be installed with `yum`. Some libraries advertise support for CentOS, which is an open source version of RHEL and is probably compatible.

For our projects to run on the VM, we need to include three files at the top level of our project.

1. `yum-requirements.txt` is a simple list of `yum` packages to install, for example:

```
python3.9
openjdk11
Tesseract 5, from https://tesseract-ocr.github.io/tessdoc/Installation.html
dotnet
```

Only include packages that are strictly required for your server to run. Most of the usual packages (Python, Java) will already be installed by the VM. For example, at the time of writing OpenCV is not compatible with Python 3.10+, so I would specify python3.9 here.

If a package is not available on `yum` and there is a Linux version available, just include a link to the package's website, like I did for Tesseract.

This text file is not run automatically — the ISYE staff install the packages manually. It just helps to have all the `yum` dependencies in one place so they (and our future labmates) know what to install.

2. `pip-requirements.txt` is a simple list of `pip` packages to install, for example:

```
matplotlib
opencv
flask
pygame
numpy==1.0.2
```

Note how I specified a NumPy version. You can follow that format for any version-specific packages. Packages without a version specified will use their latest version.

The `pip` requirements are installed automatically from this text file.

If you are not using `pip` or Python, this file can be ignored. Email the ISYE helpdesk if you need anything else installed.

3. `run.sh` is a bash script that the VM will run to start the webserver, for example:

```
python webserver.py
```

You can test this bash file on your local machine.

We use this `run.sh` file so that launching is standardized across all projects, and so that you can set up environment variables or command line arguments if you would like. For example, if I set up my webserver to take in a `demo=` parameter to decide whether to skip the consent form, my `run.sh` could look like:

```
python webserver.py demo=true
```

It is common to use environment variables to indicate which port the server should listen on. I strongly recommend doing this because ISYE will give you a port to use for your server. To set the `PORT` environment variable, for example:

```
export PORT=567
python webserver.py
```

In `webserver.py`, the variable can be accessed by:

```
port = int(os.getenv("PORT", default=80))  # gets the PORT env var, or defaults to 80
```

Remember that the `run.sh` script does not have `sudo` access! You should not need sudo access for anything, and if you do, restructure your project so you do not.

When your project is clean and you have tested your `run.sh` script, make a new branch called `production`. This branch will be the code that is deployed, so in the future if you update `main` and are ready for the changes to be published, simply rebase the `production` branch onto `main` (or delete the `production` branch and remake it).


### Getting an ISYE GitHub repo and domain name

ISYE wants their VM to pull from ISYE's GitHub to make management cleaner on their end. As shown in the projects table, each project has a repo with CEC and a repo with ISYE. 

ISYE IT is very responsive, and as a relatively small IT group, you can walk to Groseclose and meet with them directly. Please run all communication through their ticketing system, `helpdesk@isye.gatech.edu`.

You need the following from ISYE:

1. An ISYE GitHub repo for your project (under https://github.gatech.edu/isye-web).

2. A public domain name for your project (e.g., tmm-hai.cec.gatech.edu).

3. A port to use for your project.

Here is an example email you could send ISYE:

*"Hi! I am a PhD student working with Karen Feigh in the Cognitive Engineering Center. We publicly host a few project demos through a VM managed by ISYE, and I am looking to add another project in the same manner.

The project is codenamed ASMM, and its codebase is located at https://github.com/gt-cec/asmm. My GT username is jkolb6.

I believe the project needs a repo on the isye-web GitHub, a domain name (ideally asmm.cec.gatech.edu), and a port to configure my project's webserver to listen on.

Would you be able to help me with this? Thanks!"*

ISYE's IT staff are lovely. If you are confused about how any of this works, first try to educate yourself (ChatGPT is very useful for general IT questions), second ask the labmates, and third ask them for clarification.

When you get the port number from ISYE, change your `run.sh` script or webserver code to use that port. Say ISYE assigns your project the port 1234. Behind the scenes, ISYE is directing traffic from the subdomain (e.g., asmm.cec.gatech.edu port 443, the default HTTPS port) to the VM at port 1234. Your webserver therefore needs to listen to port 1234.


### Link the CEC repo to the ISYE repo

At this point you have a codebase that is ready to deploy to ISYE's infrastructure, you know what port your server needs to listen on, and you have a GitHub repo on the `isye-web` organization.

All that is left is to set up the `production` branch of your CEC repo to automatically mirror to the ISYE repo.

To do this, we will use GitHub Actions. GitHub Actions is a service where you can trigger GitHub to launch a small VM and run a script (called a "Workflow").

In our case, we want a Workflow that is triggered when we push to the CEC repo's `production` branch, and that has the VM:

1. Clone the `production` branch of the CEC repo.
2. Push it to the `main` branch of the ISYE repo.

Lucky for you, 99% of that work is already done.

In your codebase, make a new file in `.github/workflows/mirror.yml` and copy in the below script. See [this repository](https://github.com/gt-cec/tmm-hai) as an example.

```


```

If your project does not already have an ISYE repo, email `helpdesk@isye.gatech.edu` and ask for a repo on the isye-web organization.

That means we need to sync our project's code to the corresponding repository 


How to set up GitHub Actions to sync with ISYE repos
