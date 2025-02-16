# NFS Handbook

# NFS Server

```bash
# ubuntu
# 安装 nfs-kernel-server
sudo apt insatll nfs-kernel-server
# 创建一个共享目录
sudo mkdir /nfs
sudo chown nobody:nogroup /nfs
sudo chmod 777 /nfs
# 设置共享目录配置文件
sudo vi /etc/exports
/nfs *(rw,sync,no_subtree_check)
# 重新加载 nfs
sudo exportfs -r
sudo systemctl restart nfs-kernel-server

# sudo systemctl stop nfs-kernel-server
# sudo systemctl stop rpcbind
# sudo systemctl stop rpcbind.socket

# centos
yum install -y nfs-utils rpcbind
chown nfsnobody:nfsnobody /nfs
sudo chmod 777 /nfs

vi /etc/exports

/nfs_webscada_data *(rw,sync,no_subtree_check)
/nfs_webscada_static *(rw,sync,no_subtree_check)

exportfs -r
```

# NFS Client

```bash
# 安装 nfs-common
sudo apt install nfs-common
# 挂载 nfs 共享目录到本地
sudo mount.ntfs {{NFS_SERVER_IPADDR}}:{{FROM_DIR}} {{TO_DIR}}
```