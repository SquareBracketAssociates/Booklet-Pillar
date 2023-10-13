## Getting Started
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew install wget --with-libressl
$ ./scripts/build.sh
# move the pillar directory to your HOME
$ cd ..
$ mv pillar ~/.pillar
export PATH="$PATH:$PILLAR_HOME"
M:    CoInterpreter VMMaker.oscog-eem.2380 uuid: c76d...
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
  rm -rf $DIRNAME
$ cd my-pillar-project
$ pillar archetype book
  pillar.conf
  file1.pillar
  file2.pillar
  _results\
    format1\
      file1.format1
      file2.format1
    format2\
      file1.format2
      file2.format2
  pillar.conf
  file1.pillar
  file2.pillar
  _results\
    html\
      file1.html
      file2.html
    pdf\
      file1.pdf
      file2.pdf
$ cp path_to_your_project
git add pillar.conf
git add index.pillar
...
/_result/