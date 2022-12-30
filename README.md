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


## Install

### Install erlide
Current version: 0.60.4.v20210610-1117
Includes:        org.erlide.kernel.feature_0.115.3.201806041948.jar

Eclipse menu: Help -> Install New Software...
Install dialog:
- Add button, enter `Erlide` as name and `https://erlide.org/update` as UR
- Select only `Erlide IDE`
- Go to Window -> Preferences -> Erlang -> Installed runtimes and add an entry

