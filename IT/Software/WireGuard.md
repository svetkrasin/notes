dDocker commands

- `docker run --cap-add=NET_ADMIN -p 51820:51820 -it --name server archlinux /bin/bash`
-  `docker start -ai server`
- 



Server setup:
```bash
pacman -Sy
pacman -S --noconfirm wireguard-tools vim sudo
useradd -m server
su server
cd ~
umask 077 && wg genkey > private
```

