# erlide experiments

## Prepare

### Use Erlang R20 later

The build scripts uses kerl, so no specific installation is needed,
unless manually building it using rebar.

```
https://www.erlang-solutions.com/downloads/
sudo apt remove esl-erlang
sudo apt install ~/Downloads/esl-erlang_25.0.4-1_ubuntu_focal_amd64.deb

Other:
sudo apt install ~/Downloads/esl-erlang_24.3.3-1_ubuntu_focal_amd64.deb
sudo apt install ~/Downloads/esl-erlang_23.3.4.5-1_ubuntu_focal_amd64.deb
```

### Java 8
```
sudo apt-get install openjdk-8-jdk
sudo update-alternatives --config java

Set path:
export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
```

### Eclipse 4.9+
https://www.eclipse.org/downloads/packages/installer
or
```
sudo snap install --classic eclipse
```
gives ~ Eclipse 2022-12 (4.26)

### Ruby
Needed by erlide_eclipse

```
> Setup rbenv
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'eval "$(~/.rbenv/bin/rbenv init - zsh)"' >> ~/.zshrc
restart shell

> Setup ruby-build
git clone https://github.com/rbenv/ruby-build.git "$(rbenv root)"/plugins/ruby-build

> Setup older ruby 2.7.1 (setup_tools.sh indicates 2.1.2 which not working..)
rbenv install 2.7.1
rbenv global 2.7.1
restart shell
```

## Install

### Install erlide
Current version: 0.60.4.v20210610-1117
Includes:        org.erlide.kernel.feature_0.115.3.201806041948.jar

Eclipse menu: Help -> Install New Software...
Install dialog:
- Add button, enter `Erlide` as name and `https://erlide.org/update` as UR
- Select only `Erlide IDE`
- Go to Window -> Preferences -> Erlang -> Installed runtimes and add an entry


## Details

### erlide_kernel

#### Findings
- Uses kerl, copied to ~/erlide_tools
- Kerl does only support 2 prior releases
- Using a older kerl fails erlang builds anyway..
- Rebar in repo: rebar 3.4.3
  OTP-16702: To enable more optimizations, BEAM files compiled with OTP 21 and earlier cannot be loaded in OTP 25.


#### Build
./build
./build test
cd eclipse
./build


### erlide_eclipse

#### Build

```
> From Jenkinsfile
./mvnw -B -U clean verify -P help,build-product -Dmaven.test.failure.ignore=true
> From Github Actions
./mvnw -B -U clean verify -P help
```

#### Interesting commits
update kernel to 0.115.3
 e63e12dc7456b2ea2108e86124037f2711d2adb3
v0.55.0 (which includes change to 0.115.3)
9965e654e82058a11bbbea64171ad5c30419bb4e


## TODO
- Avoid commit in eclipse/build.sh
- Add otp23 and 24 as debugger target
- Replace rebar_localdep plugin (results in warnings, and crash?)
- Lift Java
- Lift ruby: erlide_eclipse/plugins/org.erlide.help/Gemfile.lock
  "lockfile was generated with 2.1.4"
  but 2.7.1 in:
  erlide_eclipse/.ruby-version
- Ruby issue
  "Gemfile:5: warning: calling URI.open via Kernel#open is deprecated, call URI.open directly or use URI#open"

