# Installing polaris on FreeBSD
You can run these instruction either in a jail or in your real system, but I only tested these in a jail.
We are going to execute polaris as an unprivilegied user called `polaris`.

## System setup
Getting the source code as the home directory of polaris:
```bash
cd /home
git clone --recursive https://github.com/agersant/polaris.git --depth=1
```

Create an unprivilegied user:
```bash
# adduser

Username: polaris
Full name: polaris
Uid (Leave empty for default):
Login group [polaris]:
Login group is polaris. Invite polaris into other groups? []:
Login class [default]:
Shell (sh csh tcsh nologin) [sh]: nologin
Home directory [/home/polaris]:
Home directory permissions (Leave empty for default):
Use password-based authentication? [yes]: no
Lock out the account after creation? [no]:
Username   : polaris
Password   : <disabled>
Full Name  : polaris
Uid        : 1001
Class      :
Groups     : polaris
Home       : /home/polaris
Home Mode  :
Shell      : /usr/sbin/nologin
Locked     : no
OK? (yes/no): yes
adduser: INFO: Successfully added (polaris) to the user database.
Add another user? (yes/no): no
Goodbye!
```

## Dependencies:
### Base

```bash
pkg install curl wget openssl git
```

### Rust
You can either use the mozilla solution:
```bash
curl https://sh.rustup.rs -sSf | sh -s -- --default-toolchain nightly
```
Or the bsd solution:
```bash
pkg install rust-nightly
# OR
cd /usr/port/lang/rust-nightly; make install
```
Whatever solution you choose keep in mind you’ll need the **nightly** compiler.
Once this is done you’ll need to add all the rust binaries to your path. For
csh you can run:
```bash
echo "set path = ($path $HOME/.cargo/bin)" >> .cshrc
source .cshrc
```

# install polaris
```bash
cd /home/polaris

# compile the code and install the binary in /home/polaris
cargo install --path . --root .
```

# configure polaris
```bash
mkdir -p /home/polaris/.config/
touch /home/polaris/.config/polaris
chown -R polaris /home/polaris
```

# daemonize polaris
```bash
wget -O /usr/local/etc/rc.d/polaris https://raw.githubusercontent.com/agersant/polaris/master/doc/freebsd/polaris.rc
chmod +x /usr/local/etc/rc.d/polaris
sysrc polaris_enable="YES"
service polaris start
```
You can also `restart` or `stop` the daemon.

## Where is the default configuration
- **configuration file**: `/home/polaris/.config/polaris
- **port**: `5050`
- **database**: ``/home/polaris/db`
- **web**: ``/home/polaris/web`

## Update polaris
```bash
git pull
cargo install --path . --root .
service polaris restart
```

## Customize your installation

- If you did not create an unprivilegied user to run polaris you can specify
which user you want to use:
```bash
sysrc polaris_user="USER"
```

- If you did not install the polaris binary your `home/polaris` you can
specify the path used:
```bash
sysrc polaris_command="EXE"
```

- If you want to move the logs somewhere else:
```bash
sysrc polaris_logfile="PATH"
```

- If you want to choose the level of logs:
```bash
sysrc polaris_loglevel="LEVEL"
```

- If you want to select
