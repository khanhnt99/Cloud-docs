# CronJob
## Job
### Giới thiệu
- `Job` là resource sử dụng container để thực hiện xử lí chỉ 1 lần.
- Resource được xử lí 1 cách song song.
- Đảm bảo thực thi container với số lần nhất định.
- Với Pod thì `Stopped = Unexpected error` nhưng với Job thì `Stopped = Normal Finnish`

### Tạo Job
- Create Job

```
apiVersion: batch/v1
kind: Job
metadata:
  name: sample-job
spec:
  completions: 1
  parallelism: 1
  backoffLimit: 10
  template:
    spec:
      containers:
      - name: sleep-container
        image: centos:latest
        command: ["sleep"]
        args: ["60"]
      restartPolicy: Never
```

```
corgi@ubuntu:~/kubernetes$ kubectl get jobs
NAME         COMPLETIONS   DURATION   AGE
sample-job   0/1           69s        67s
corgi@ubuntu:~/kubernetes$ kubectl get jobs
NAME         COMPLETIONS   DURATION   AGE
sample-job   1/1           80s        94s
```

```
 Completions-số lần thực thi là 1
 parallelism-số lần thực thi song song là 1
 backoffLimit là số lần cho phép thất bại
```

```
corgi@ubuntu:~/kubernetes$ kubectl get pods
NAME                                READY   STATUS      RESTARTS   AGE
mysql-1592048710-6d7c7b5c67-nxqmz   0/1     Pending     0          105d
sample-job-w95cn                    0/1     Completed   0          4m31s
```

## CronJob
- 1 CronJob sẽ tạo Jobs chạy theo thời gian được thiết lập.
- 
