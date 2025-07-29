# Installation

https://liquidprompt.readthedocs.io/en/stable/install.html

Clone the project
```
git clone --branch stable https://github.com/liquidprompt/liquidprompt.git ~/opt/liquidprompt
```

~/.bashrc file
```
echo 'Init liquidprompt'
# Only load Liquid Prompt in interactive shells, not from a script or from scp
if [[ $- = *i* ]]; then
  source ~/opt/liquidprompt/liquidprompt
  source ~/opt/liquidprompt/themes/powerline/powerline.theme
  lp_theme powerline
fi
```
