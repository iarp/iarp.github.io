# Deploying Django on Windows

Instructions based on Windows Server 2008 R2, Server 2012 or Windows 7 as those are my only tested so far.

Ported from https://www.mattwoodward.com/2016/07/23/running-a-django-application-on-windows-server-2012-with-iis/

## Requirements

### Assumptions

* Master Python will be installed to `C:\Pyton36\`
* The virtual environment will be in `C:\Apps\virtualenvs\`
* The django root will be placed in `C:\Apps\django_forms\`

### Required Installations

* Python 3.6.x latest version: https://www.python.org/downloads/windows/
    * Choose Customize Installation
    * Uncheck Documentation, "tcl/tk and IDLE", "Python test suite"
    * Supply `C:\Python36` to Customize install location box.
* Git latest version: https://git-scm.com/
    * Latest version is not necessary, any version will do as long as it works.
* IIS must be enabled
    * Start -> Control Panel -> Programs and Features -> (left sidebar) Turn Windows features on or off
    * Internet Information Services, make sure the following items are checked or are blue filled:
        * Web Management Tools
        * World Wide Web Services
    * Click OK, wait for installation to finish
* ODBC Drivers
    * After you clone the repo, there will be a file to install found within the repo.


## Virtual Environments

### Test basic installation

Skip this if you don't want to test an installation of python and django at its most basic.

Install the file downloaded in step 5 above then:

* Make the directory C:\Apps\virtualenvs
* Open command prompt, you may want to open it as administrator

```bash
cd C:\Python36
python.exe -m pip install --upgrade pip
cd Scripts
pip.exe install virtualenvs
cd C:\Apps\virtualenvs
C:\Python36\Scripts\virtualenv.exe test_proj
test_proj\Scripts\activate.bat
pip install django
cd C:\Apps
django-admin startproject test_proj
cd test_proj
python manage.py migrate
python manage.py runserver
```

* Open your browser to http://127.0.0.1:8000/ and make sure you can see a website running saying the installation worked.
* CTRL+C a couple times to stop it running and get back to standard prompt

```bash
deactivate # this is a special command, just type it like that.
```

## New Environment

```bash
cd C:\Apps\virtualenvs
C:\Python36\Scripts\virtualenv.exe django_forms
```

## Activating Environments

```bash
django_forms\Scripts\activate.bat
# Your console command line should now be prepended with (django_forms)
```

## Cloning the repo

Within command prompt:

```bash
cd C:\Apps
git clone <REPO_URL> django_forms
pip install -r requirements.txt
```

# Configuring IIS

## Configure FastCGI

* Click on your servers name on the left sidebar
* Double click FastCGI Settings in the main window
* Click Add Application on the right sidebar
    * Full Path: `C:\Apps\virtualenvs\django_forms\Scripts\python.exe`
    * Arguments: `C:\Apps\virtualenvs\django_forms\Scripts\wfastcgi.exe`
    * Monitor changes to file: `C:\Apps\django_forms\manage.py`
    * Activity Timeout: 300
    * Request Timeout: 300
    * Click on Environment Variables line and then click the ... box that appears and add the following Members

```bash
Name: DJANGO_SETTINGS_MODULE Value: django_forms.settings_prod 
Name: PYTHONPATH Value: C:\Apps\django_forms 
Name: WSGI_HANDLER Value: django.core.wsgi.get_wsgi_application()
```

## Configure A New Site

* Click on your servers name on the left sidebar
* Click on Sites
* Click on Add Website... on the right sidebar

```bash
Site name: what ever you want
Physical path: **C:\Apps\django_forms\**
Configure Binding as needed or leave blank for all
Click OK
```

## Link FastCGI To Site

* Find the site in the left sidebar and click on it
* Click on Handler Mappings in the main window
* Click Add Module Mapping on the right sidebar
    * Request path: *
    * Module: FastCgiModule
    * Executable: `C:\Apps\virtualenvs\django_forms\Scripts\python.exe|C:\Apps\virtualenvs\django_forms\Scripts\wfastcgi.exe`
        * Note that is a Pipe character separator without space
    * Name: Django handler or whatever you want
    * Click Request Restrictions
        * Uncheck Invoke handler only...
        * Click OK
    * Click OK
    * If prompted with "Do you want to create a FastCGI application for this executable?" click No as we have already created it
