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

Maven is the used build tool, see `~/.m2` for cached packages.
```

### Eclipse 4.9+
https://www.eclipse.org/downloads/packages/installer

https://wiki.eclipse.org/Eclipse/Installation/
or
```
sudo snap install --classic eclipse
```
gives ~ Eclipse 2022-12 (4.26)

Installed plugins can be located at:
`~/snap/eclipse/66/amd64`

Start with extra logs
`eclipse -debug -consoleLog`

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

Eclipse menu: Help -> Install New Software...
Install dialog:
- Add button, enter `Erlide` as name and `https://erlide.org/update` as UR
- Select only `Erlide IDE`
- Go to Window -> Preferences -> Erlang -> Installed runtimes and add an entry

```
Current version: 0.60.4.v20210610-1117
Includes:        org.erlide.kernel.feature_0.115.3.201806041948.jar
```

## Details

### erlide_kernel

#### Findings
- Uses kerl, copied to ~/erlide_tools
- Kerl does only support 2 prior releases
- Using a older kerl fails erlang builds anyway..
- Rebar in repo: rebar 3.4.3
  OTP-16702: To enable more optimizations, BEAM files compiled with OTP 21 and earlier cannot be loaded in OTP 25.


#### Build
```
./build
./build test
cd eclipse
./build
```

The build result is found at:
`eclipse/org.erlide.kernel.site-<VERSION>.zip`

which can be unzipped to the update site:

`update/kernel/<VERSION>/`

Like `update/kernel/0.115.5/`


### erlide_eclipse

#### Build

> Use OTP23/24 if testing older erlide versions

```
> Build via makefile
make build

> Build and ignore test failures
./mvnw -B -U enforcer:display-info clean verify -Phelp --fail-at-end
./mvnw -B -U enforcer:display-info clean verify -Phelp -Dmaven.test.failure.ignore=true --fail-at-end

> Deploy/Publish via makefile
make publish

> From Jenkinsfile
./mvnw -B -U clean verify -P help,build-product -Dmaven.test.failure.ignore=true

> From Github Actions
./mvnw -B -U clean verify -P help

> Build without running tests (~49s)
./mvnw -B -U clean -P help

> Build with full logs: -X
```

#### Pre-release
```
> Set version manually using:
git grep -l '0.61.1' | xargs sed -i 's/0.61.1/0.62.0/g'

> Build erlide_eclipse
make build

> Copy result to the update site:
cd erlide.github.io/update/prerelease/
cp ../../../erlide_eclipse/releng/org.erlide.site/target/org.erlide-*.zip .


> Replace files
git rm -r features/ plugins/
unzip org.erlide-*.zip
> Replace all [A]
rm org.erlide-*.zip
git add .
```

#### jinterface

Not using OTPs lib/jinterface/priv/OtpErlang.jar

Update:
- Copy source:
  cp ~/.kerl/builds/25.2/otp_src_25.2/lib/jinterface/java_src/com/ericsson/otp/erlang/* libs/com.ericsson.otp.jinterface/src/com/ericsson/otp/erlang/
- Update version, Use version from:
  ~/.kerl/builds/25.2/otp_src_25.2/lib/jinterface/vsn.mk:JINTERFACE_VSN = 1.13.1
- Update version in files:
  libs/com.ericsson.otp.jinterface/pom.xml
  libs/com.ericsson.otp.jinterface/META-INF/MANIFEST.MF

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
- Upstream warning fixes to Erlang JInterface
- Update kernel version banner on https://erlide.org/ (via metafile?)

