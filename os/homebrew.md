```
## 先使用 gitee 将 brew 代码克隆
export HOMEBREW_BREW_GIT_REMOTE="git@gitee.com:renguangli_1/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="git@gitee.com:renguangli_1/homebrew-core.git"

## 各种报错
/bin/bash -c "$(curl -fsSL https://gitee.com/renguangli_1/install/blob/master/install.sh)"
```



1. clone brew

```
git clone git@gitee.com:renguangli_1/brew homebrew
eval "$(homebrew/bin/brew shellenv)"
brew update --force --quiet
chmod -R go-w "$(brew --prefix)/share/zsh"

cd /opt/homebrew/Library/Taps/homebrew 
git clone git@gitee.com:renguangli_1/homebrew-services.git
git clone git@gitee.com:renguangli_1/homebrew-cask.git
```