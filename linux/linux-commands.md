ps aux
ps aux | grep <service>
top
htop
systemctl status <service>
systemctl is-active <service>
systemctl list-units --type=service
journalctl -u <service>
journalctl -u <service> -n 20
journalctl -xe
df -h
du -sh *
ls -l
chmod
chown
tail -f <file>
echo "text" >> file
cp
mkdir
touch
uptime
curl -I localhost
ssh -i <key.pem> user@host
