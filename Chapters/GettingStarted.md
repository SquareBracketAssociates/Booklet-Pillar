## Getting Started@chagettingstartedIn this chapter we give the basic steps to get you started with your first Pillar project.We show first how to install Pillar in different platforms and how to test your installation.This chapter covers also the installation of LaTeX, which is required to generate pdf documents.### Building Pillar from SourcesThis section explains how to build and install Pillar both in Unix based systems \(like OSX and Linux\) and Windows.The first part of this section covers how to download an already built version of pillar or how to build one yourself.#### Build RequirementsBuilding Pillar from sources requires that you have some dependencies installed in your system:- **Git**: a Git installation is required to get the Pillar repository from:```git@github.com:pillar-markup/pillar.git```- **wget**: is required to do several file downloads. For OSX users, you might need to install `wget` via `brew` following for example the following instructions:```# The following lines will install both Homebrew and wget, ignore the first one if Homebrew is already installed
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew install wget --with-libressl```!!note Windows is not able to deal with `.sh` and makefile scripts natively, so you have to install tools providing shell-like capabilities such as [https://www.cygwin.com/](https://www.cygwin.com/). If you decide to install cygwin you should be able to follow the Mac OS X and Linux installation section.#### Downloading and Building PillarYou can build Pillar from sources on Unix-like systems such as OSX and Linux.This approach is also valid using msys and cygwin like terminals on Windows.To get Pillar source code in latest development version \(v7.3.0\) we can use a `git clone` command as follows:```$ git clone git@github.com:pillar-markup/pillar.git -b v7.3.0```Once we got the repository, we should execute the `build.sh` script found in the _scripts_ directory:```$ cd pillar
$ ./scripts/build.sh```#### Installing it in your SystemYou can then proceed to install that pillar build where you want.For example, you can place it in a hidden directory in your home directory:```# Go back to the previous directory and 
# move the pillar directory to your HOME
$ cd ..
$ mv pillar ~/.pillar```Then add that directory to the PILLAR\_HOME and HOME environment variables, for example, by modifying your .bashrc \(or .zshrc\) with:```export PILLAR_HOME="$HOME/.pillar/build"
export PATH="$PATH:$PILLAR_HOME"```#### Test your InstallationTo test your pillar installation, open a new terminal and execute the pillar `--version command`. If everything is ok, that should print out \(as in the current version\) the version of the Pharo VM. For example:```$ pillar --version
M:    CoInterpreter VMMaker.oscog-eem.2380 uuid: c76d...```% We might want to add a paragraph showing if the building part works here### Getting Pillar DependenciesIf you're going to build pdf documents using Pillar, then you will required to install LaTeX also.We recommend that you install a complete TeX Live installation, like that different LaTeX templates can reuse existing modules.You can get a full installation from [https://tug.org/texlive/](https://tug.org/texlive/) or by using your favorite package manager on Linux.For example using apt-get you can do:```sudo apt-get install texlive-full```Also, a manual LaTeX installation can be done by downloading texlive's default distribution as a tarball and installing several packages on top. This installation procedure is already distributed with Pillar as it is used by our continuous integration jobs. You can find our installation script in the `scripts/ci/ensureLatex.sh` file that you can find in:```scripts/ci/ensureLatex.sh``````  #!/bin/bash
  # From https://github.com/y-yu/install-tex-travis/blob/master/install-tex.sh

  #Enable to not exit on error
  #set -o errexit

  #Enable to trace bash execution
  #set -o xtrace

  DIRNAME=tl-`date +%Y_%m_%d_%H_%M_%S`

  echo "make the install directory: $DIRNAME"
  mkdir $DIRNAME
  cd $DIRNAME

  wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
  tar zxvf install-tl-unx.tar.gz
  cd install-tl-*

  BASE_PROFILE=$(cat << EOS
  selected_scheme scheme-small
  TEXDIR $HOME/texlive/2017
  TEXMFCONFIG $HOME/.texlive2017/texmf-config
  TEXMFHOME $HOME/texmf
  TEXMFLOCAL $HOME/texlive/texmf-local
  TEXMFSYSCONFIG $HOME/texlive/2017/texmf-config
  TEXMFSYSVAR $HOME/texlive/2017/texmf-var
  TEXMFVAR $HOME/.texlive2017/texmf-var
  option_doc 0
  option_src 0
  EOS
  )

  if [[ $TRAVIS_OS_NAME == 'osx' ]]; then
    echo "$BASE_PROFILE\nbinary_x86_64-darwin 1" > ./small.profile
    export PATH=$PATH:$HOME/texlive/2017/bin/x86_64-darwin
  else
    echo "$BASE_PROFILE\nbinary_x86_64-linux 1" > ./small.profile
    export PATH=$PATH:$HOME/texlive/2017/bin/x86_64-linux
  fi

  ./install-tl -profile ./small.profile
  tlmgr init-usertree
  tlmgr install latexmk
  tlmgr install luatex85

  cd ../..

  echo "remove the install directory"
  rm -rf $DIRNAME```Now you are ready to write your document in Pillar and get them exported in PDF and HTML. Once the basic installation is installed you can procceed to install all your required dependencies using TeXLives `tlmgr` package manager. You'll find an up-to-date bash script that performs the installation of the minimal required packages in:```scripts/ci/ensure\_book\_dependencies.sh```This script installs packages to manage fonts and better LaTeX rendering such as fira, gentium-tug, footmisc, tcolorbox, environ, trimspaces, import, multirow and ifetex.### A First Pillar ProjectA Pillar project is a directory containing several `.pillar` files with your content, a `pillar.conf` configuration file and a `_support` directory containing several template-related files which will manage the look-and-feel of your document.All project structure is setup by Pillar itself, you do not need to do it manually.#### Setup the ProjectLet's then procceed to create a new empty directory for our project and tell Pillar that we want to use the book archetype on it.```$ mkdir my-pillar-project
$ cd my-pillar-project
$ pillar archetype book```The `archetype` command sets up the project structure for you: it installs a configuration file _pillar.conf_ with default values, it creates several Pillar files with some sample content, and it imports the basic styling files inside _\_support_.#### Build your ProjectAt this point, you can already generate your book using the command `pillar build [output_format]` followed by the desired output. For example, to generate your project in html:```$ pillar build html```Or in pdf using your system's LaTeX installation:```$ pillar build pdf```#### Inspect your Project's OutputOnce `build` has finished, the build results are written into the `_result` directory.The `_result` directory contains the output in different subdirectories depending on the used format, and is structured as follows:```your_project\
  pillar.conf
  file1.pillar
  file2.pillar
  _results\
    format1\
      file1.format1
      file2.format1
    format2\
      file1.format2
      file2.format2```For example, if you generated your files in html and pdf you will see```your_project\
  pillar.conf
  file1.pillar
  file2.pillar
  _results\
    html\
      file1.html
      file2.html
    pdf\
      file1.pdf
      file2.pdf```#### Building a Single FilePillar generates all `.pillar` files that it finds in your project by default.We can instruct it otherwise by specifying the files to build as argument.For example, if you want to build only `index.pillar` to html you can use the `pillar build` command as follows:```$ pillar build html index.pillar```this will generate only the output related to the `index.pillar` file.### Versionning your ProjectIn this section we explain how to share your project in a source code repository such as Git.This section guides you through the setup of a Git repository, the selection of the correct files to commit, and the creation of a `.gitignore` file to avoid auto-generated files.#### Setting up a Git RepositoryThere are two main ways to setup a Git repository:- we can clone an existing repository and add our files there, or- we can create a new repository and push it to an existing remoteThis first option is the more practical in case you previously created a Git repository in GitHub.If that is the case, or if you prefer to start by creating a repository on GitHub or your favorite Git repository hosting, then this step consists on cloning that existing repository in a new directory and adding your files to it.```$ git clone git@github.com:[your_ursername]/[your_repository].git
$ cp path_to_your_project```#### Versioning your BookNow to version simply you book you should version all the files except `_result`. Later we will show you how to add travis and bintray support for automatic compilation so that you do not need to install locally LaTeX on your machine. ```git add _support
git add pillar.conf
git add index.pillar
...```#### Adding a .gitignoreNot all the downloaded files are worth versionning. We suggest the following .gitignore configuration. ```more .gitignore
/_result/```You can remove the folder figures and the chapter samples. Now you are ready to commit your files. 