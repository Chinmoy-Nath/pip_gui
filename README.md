# pip_tkinter
A GUI for "pip"

# How to run the GUI

1. Clone the repo from here : https://github.com/upendra-k14/pip_gui.git , Otherwise do :
   `git clone https://github.com/upendra-k14/pip_gui.git <targetlocation>`
2. Navigate to the `<targetlocation>/pip_gui/` folder
3. From inside the pip\_gui folder : `python -m pip_tkinter`

# Motivation
This project is intended to provide a GUI version for "pip". It is currently a command line based package manager used to install and manage software packages written in Python. But, many users are not familiar with the command line and thus find difficulties using and accessing PIP. People who still don’t have enough exposure to command line tools(specially in case of Windows) find installing packages using PIP very cumbersome. Therefore, the main motivation behind this project is to make various functionalities provided by PIP accessible to Windows/LINUX/Mac based users through a tkinter based GUI interface which could be further integrated with IDLE. As a whole, it would help beginners to focus more on learning and using new Python packages rather than getting in unavoidable trouble of configuring various paths and configurations in order to install new Python Packages from PyPI.

The product developed will be a GUI for pip to make various functionalities of pip ( a command line tool for installing Python packages) easily accessible to users. Main motivation behind the need for GUI version of Python Package Manager is :

* Make various functionalities provided by PIP easily accessible to Windows/LINUX/Mac based users 
* Help people to focus only on fulfilling the task of installing Python packages rather than getting in unavoidable trouble of configuring various paths, versions and configurations

# Target Users

* Windows Users ( who have difficulty using command prompt )
* Novice Python users ( on MacOS or Linux )

# Targeted Tasks

* Search, select version and install package 
* Check for new updates and install them
* Uninstall a package
* Support different installation methods :
  * Requirements.txt files
  * .whl files
  * From local archives
  * From Pythonlibs

# Design Principles

The design of the pip gui should adhere to these principles :

* User goals and preferences should be completed by through the application
* Use cases should be clearly identified
* UI should be self explanatory
* UI should be task centric, not feature centric
* The UI elements should be consistent, not over-creative

# TODO list

* write tests
* fix issues
* create docs

# Bug Tracker

* http://bugs.python.org/issue27051
