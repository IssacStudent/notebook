# 常用命令详解

```shell
  attach      Attach to a running container 	      # 当前shell下attach连接指定运行的镜像
  build       Build an image from a Dockerfile        # 通过Dockerfile定制镜像
  commit      Create a new image from a container changes  #提交当前容器为新的镜像
  cp          Copy files/folders between a container and the local filesystem #从容器中拷贝指定文件或目录到宿主机中
  create      Create a new container 				  # 创建一个新的容器，同run,但不启动容器
  diff        Inspect changes to files or directories on a container's filesystem #查看docker容器的变化
  events      Get real time events from the server 	  # 从docker服务获取容器实时事件
  exec        Run a command in a running container    # 在已存在的容器上运行命令
  export      Export a container filesystem as a tar archive # 导出容器的内容流作为一个tar归档文件[对应import]
  history     Show the history of an image            # 展示一个镜像形成历史
  images      List images                             # 列出系统当前的镜像
  import      Import the contents from a tarball to create a filesystem image # 从tar包中的内容创建一个新的文件系统镜像[对应export]
  info        Display system-wide information         # 显示系统相关信息
  inspect     Return low-level information on Docker objects # 查看容器详细信息
  kill        Kill one or more running containers     # 杀死指定的docker容器
  load        Load an image from a tar archive or STDIN # 从一个tar包加载一个镜像[对应save]
  login       Log in to a Docker registry			  # 注册或者登录一个docker源服务器
  logout      Log out from a Docker registry		  # 从当前Docker registry退出
  logs        Fetch the logs of a container			  # 输出当前容器日志信息
  pause       Pause all processes within one or more containers 	     # 暂停容器
  port        List port mappings or a specific mapping for the container # 查看映射端口对应容器内部源端口
  ps          List containers						  # 列出容器列表
  pull        Pull an image or a repository from a registry # 从docker镜像源服务器拉取指定镜像或库镜像
  push        Push an image or a repository to a registry   # 推送指定镜像或者库镜像至docker源服务器
  rename      Rename a container					  # 给docker容器重新命名
  restart     Restart one or more containers		  # 重启运行的容器
  rm          Remove one or more containers			  # 移除一个或者多个容器
  rmi         Remove one or more images				  # 移除一个或者多个镜像[无容器使用该镜像时才可删除，否则需删除相关容器才可继续或 -f 强制删除]
  run         Run a command in a new container		  # 创建一个新的容器并运行一个命令
  save        Save one or more images to a tar archive (streamed to STDOUT by default) # 保存一个镜像为一个tar包[对应load]
  search      Search the Docker Hub for images		  # 在docker hub中搜索镜像
  start       Start one or more stopped containers	  # 启动容器
  stats       Display a live stream of container(s) resource usage statistics # 实时显示容器资源使用统计
  stop        Stop one or more running containers	  # 停止容器
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE # 给源中镜像打标签
  top         Display the running processes of a container   	  # 查看容器中运行的进程信息
  unpause     Unpause all processes within one or more containers # 取消暂停容器
  update      Update configuration of one or more containers	  # 更新一个或多个容器配置
  version     Show the Docker version information	  # 查看docker版本号 
  wait        Block until one or more containers stop, then print their exit codes # 截取容器停止时的退出状态值
```

