通过WSL Docker 启动一个轻量级的 **k3s 集群**（单节点或多节点）非常简单，以下是详细步骤：

------

## 方法1. 单节点 k3s（最快体验）

### **步骤**

1. **运行 k3s Server（控制平面 + 工作节点）**：

   ```bash
   docker run -d --name k3s-server \
     --privileged \
     --tmpfs /run \
     --tmpfs /var/run \
     -e K3S_KUBECONFIG_MODE="644" \
     -p 6443:6443 \
     -p 80:80 \
     -p 443:443 \
     rancher/k3s:latest \
     server --disable traefik
   ```

   - •`--privileged`：赋予容器特权（k3s 需要）。
   - •`--tmpfs`：避免文件系统权限问题。
   - •`-p 6443:6443`：暴露 Kubernetes API 端口。
   - •`--disable traefik`：禁用内置的 Traefik Ingress（可选）。

   

2. **获取 kubeconfig**

   ```bash
   docker cp k3s-server:/etc/rancher/k3s/k3s.yaml ~/.kube/config
   sed -i 's/127.0.0.1/localhost/g' ~/.kube/config
   ```

   

3. **验证集群**：

   ```bash
   laomei@wsl:~$ kubectl get no
   NAME           STATUS   ROLES                  AGE     VERSION
   8f9eb5455ff6   Ready    control-plane,master   7h26m   v1.31.12+k3s1
   ```

   

   

------

## **方法 2：多节点 k3s 集群（Server + Agent）**

### **步骤**

1. **启动 Server 节点**： 

   ```bash
   docker run -d --name k3s-server \
     --privileged \
     --tmpfs /run \
     --tmpfs /var/run \
     -e K3S_KUBECONFIG_MODE="644" \
     -p 6443:6443 \
     rancher/k3s:latest \
     server --cluster-init
   ```

   

2. **获取 Server 的 Token**：

   ```bash
   docker exec k3s-server cat /var/lib/rancher/k3s/server/node-token
   ```

   输出示例：

   ```bash
   K10a1b2c3d4e5f6...::server:abcdef1234567890
   ```

3. **启动 Agent 节点**（连接到 Server）：

   ```bash
   docker run -d --name k3s-agent \
     --privileged \
     --tmpfs /run \
     --tmpfs /var/run \
     -e K3S_URL="https://<SERVER_IP>:6443" \
     -e K3S_TOKEN="<TOKEN_FROM_STEP_2>" \
     rancher/k3s:latest \
     agent
   ```

   - •替换 `<SERVER_IP>`为 Server 容器的 IP（通过 `docker inspect k3s-server | grep IPAddress`获取）。
   - •替换 `<TOKEN_FROM_STEP_2>`为步骤 2 的 Token。

4. **验证多节点集群**：

   ```bash
   kubectl get nodes
   ```

   输出示例：

   ```bash
   NAME         STATUS   ROLES                  AGE   VERSION
   k3s-server   Ready    control-plane,master   5m    v1.28.5+k3s1
   k3s-agent    Ready    <none>                1m    v1.28.5+k3s1
   ```

   

------

## **常见问题**

### **1. 如何访问 Kubernetes Dashboard？**

k3s 默认不安装 Dashboard，可以手动安装：

```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
kubectl proxy --port=8080
```

访问：`http://localhost:8080/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/`

### **2. 如何持久化数据？**

挂载卷到 `/var/lib/rancher/k3s`：

```
docker run -d --name k3s-server \
  -v /path/on/host:/var/lib/rancher/k3s \
  ...其他参数...
```

### **3. 如何卸载 k3s？**

```
docker stop k3s-server k3s-agent
docker rm k3s-server k3s-agent
sudo rm -rf /var/lib/rancher/k3s
```

------

## **总结**

| 方法       | 适用场景     | 关键命令/配置                       |
| ---------- | ------------ | ----------------------------------- |
| **单节点** | 快速测试     | `docker run ... rancher/k3s server` |
| **多节点** | 模拟生产环境 | 需共享 `K3S_TOKEN` 和 `K3S_URL`     |

