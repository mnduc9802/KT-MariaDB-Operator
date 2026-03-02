# 1. MariaDB Operator là gì?

MariaDB Operator là một Kubernetes Operator dùng để tự động quản lý MariaDB cluster trong Kubernetes. Operator sử dụng Custom Resource (CR) để định nghĩa database và tự động xử lý:

- Deploy MariaDB cluster

- Replication

- Backup / Restore

- Failover

- Scaling

- Monitoring

Operator hoạt động dựa trên Operator Pattern trong Kubernetes:

→  Chỉ cần khai báo YAML resource, operator sẽ tự tạo các resource Kubernetes cần thiết.

# 2. Các thành phần của MariaDB Operator

Khi triển khai MariaDB Operator trong Kubernetes sẽ có các thành phần chính sau:

## 2.1. Operator Controller

Deployment chính của operator.

Pod:

mariadb-operator-controller-manager

Chức năng:

- Watch các Custom Resources

- Tạo hoặc cập nhật các resource tương ứng

- Điều khiển lifecycle database

Operator sẽ watch:

```
MariaDB
MaxScale
Backup
Restore
User
Database
Grant
```

## 2.2. CRDs (Custom Resource Definitions)

CRD định nghĩa resource mới trong Kubernetes.

Sau khi cài operator:

```
kubectl get crds | grep mariadb
```

Có thể thấy:

```
mariadbs.k8s.mariadb.com
maxscales.k8s.mariadb.com
backups.k8s.mariadb.com
restores.k8s.mariadb.com
users.k8s.mariadb.com
databases.k8s.mariadb.com
grants.k8s.mariadb.com
```

Các CRD này cho phép tạo resource:

```
kind: MariaDB
kind: MaxScale
kind: Backup
```

## 2.3. Webhook

MariaDB operator sử dụng admission webhook để:

- Validate CR

- Default values

Pod:

```
mariadb-operator-webhook
```

Nếu webhook lỗi → kubectl apply mariadb.yaml sẽ fail

## 2.4. Cert Controller

Pod:

```
mariadb-operator-cert-controller
```

Chức năng:

- Tạo certificate cho webhook

- Quản lý TLS secret

## 2.5. MariaDB Cluster

Sau khi apply resource:

```
kind: MariaDB
```

Operator sẽ tạo:

```
StatefulSet
Service
PVC
ConfigMap
Secret
```

Ví dụ:

```
mariadb-0
mariadb-1
mariadb-2
```

## 2.6. MaxScale (optional)

MaxScale là database proxy / load balancer cho MariaDB.

Chức năng:

- Read/Write splitting

- Failover

- Connection pooling

- Query routing

Operator có CR:

```
kind: MaxScale
```

Sau khi apply sẽ tạo:

```
Deployment maxscale
Service maxscale
```
