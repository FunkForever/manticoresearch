# Starting Manticore in Linux

When Manticore Search is installed using DEB or RPM packages, the searchd process can be run and managed by operating system's init system. Most Linux versions now use systemd, while older releases use SysV init.

If you are not sure about the type of the init system your platform use, run:

```shell
ps --no-headers -o comm 1
```

### Starting and stopping using systemd

After the installation the Manticore Search service is not started automatically. To start Manticore run the following command:

```shell
sudo systemctl start manticore
```

To stop Manticore run the following command:


```shell
sudo systemctl stop manticore
```

The Manticore service is set to run at boot. You can check it by running:

```shell
sudo systemctl is-enabled manticore
```

If you want to disable Manticore starting at boot time run:

```bash
sudo systemctl disable manticore
```

To make Manticore start at boot, run:

```bash
sudo systemctl enable manticore
```

In some newer operating systems it can fail with error "Failed to enable unit: Unit ... is transient or generated.". In this case you can remove the generator and try again. It's unlikely you need the generator ever again after Manticore is installed.

In Debian-based operating systems run:
```bash
sudo rm /lib/systemd/system-generators/manticore-generator
sudo systemctl daemon-reload
```

In CentOS and RHEL run:
```bash
sudo rm /usr/lib/systemd/system-generators/manticore-search-generator
sudo systemctl daemon-reload
```

If you still need the generator again just install the Manticore packages again and it will recover the generator file.

`searchd` process logs startup information in `systemd` journal. If `systemd` logging is enabled you can view the logged information with the following command:

```shell
sudo journalctl --unit manticore
```

### Custom startup flags using systemd

`systemctl set-environment _ADDITIONAL_SEARCHD_PARAMS` allows you to specify custom startup flags Manticore Search daemon should be started with. See full list [here](../Starting_the_server/Manually.md#searchd-command-line-options).

For example, to start Manticore with debug logging level you can run:
```bash
systemctl set-environment _ADDITIONAL_SEARCHD_PARAMS='--logdebug'
systemctl restart manticore
```

To undo it run:
```bash
systemctl set-environment _ADDITIONAL_SEARCHD_PARAMS=''
systemctl restart manticore
```

Note, systemd environment variables get reset on server reboot.

### Starting and stopping using service

Manticore can be started and stopped using service commands:

```shell
sudo service manticore start
sudo service manticore stop
```

To enable the sysV service at boot on RedHat systems run:

```shell
chkconfig manticore on
```

To enable the sysV service at boot on Debian systems (including Ubuntu) run:

```shell
update-rc.d manticore defaults
```

Please note that `searchd` is started by the init system under  `manticore` user and all files created by the server will be owned by this user. If `searchd` is started under ,for example, root user, the permissions of files will be changed which may lead to issues when running again `searchd` as service.
