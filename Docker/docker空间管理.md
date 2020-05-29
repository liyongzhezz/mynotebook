```bash
# 查看空间
# docker system df
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              11                  9                   1.084GB             202.9MB (18%)
Containers          18                  12                  10.61kB             0B (0%)
Local Volumes       0                   0                   0B                  0B
Build Cache         0                   0                   0B                  0B

# 清理文件系统
# docker system prune
WARNING! This will remove:
  - all stopped containers
  - all networks not used by at least one container
  - all dangling images
  - all dangling build cache

Are you sure you want to continue? [y/N]

# 强制清理，忽略依赖关系。
# docker system prune -a -f

# 清理所有状态为 <none> 的 image
# docker image prune
WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N]

# 强制清理 image，忽略依赖关系。
# docker image prune -a
WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N]

# 清理无用数据卷
# docker volume ls
# docker volume prune
WARNING! This will remove all local volumes not used by at least one container.
Are you sure you want to continue? [y/N]

# 清理所有停止的容器
# docker container prune
WARNING! This will remove all stopped containers.
Are you sure you want to continue? [y/N]
```

