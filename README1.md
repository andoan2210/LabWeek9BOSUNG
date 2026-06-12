# Lab: Installing CloudWatch Agent on EC2

## Mục tiêu

Cài đặt CloudWatch Agent trên EC2 để thu thập custom metrics như:

* Memory usage
* Disk usage

---

## Môi trường

| Thành phần     | Thông tin               |
| -------------- | ----------------------- |
| Cloud Provider | AWS                     |
| Services       | EC2, CloudWatch         |
| Region         | us-east-1               |
| OS             | Ubuntu                  |
| Instance Name  | Tes_Lab                 |
| Agent          | Amazon CloudWatch Agent |

---

## Bước 1: Tạo EC2 Instance

Tạo một EC2 instance dùng cho lab và kết nối vào instance bằng EC2 Instance Connect.

![EC2 Instance](/images/01-ec2-instance.png)

---

## Bước 2: Cài đặt CloudWatch Agent

Cài CloudWatch Agent trên Ubuntu bằng file `.deb`.

```bash
sudo apt-get update -y
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
sudo dpkg -i amazon-cloudwatch-agent.deb
```

Kiểm tra agent đã được cài đặt:

```bash
ls /opt/aws/amazon-cloudwatch-agent/bin/
```

![Install CloudWatch Agent](/images/02-install-agent.png)

---

## Bước 3: Cấu hình và chạy CloudWatch Agent

Chạy Configuration Wizard:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```

Sau đó start CloudWatch Agent:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl \
-a fetch-config \
-m ec2 \
-c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json \
-s
```

Kiểm tra trạng thái agent:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status
```

Kết quả:

```json
{
  "status": "running"
}
```

![Agent Status](/images/03-agent-status.png)

---

## Bước 4: Kiểm tra Metrics trên CloudWatch

Vào CloudWatch theo đường dẫn:

```text
CloudWatch → Metrics → All metrics → CWAgent
```

Các custom metrics đã xuất hiện:

* `mem_used_percent`
* `disk_used_percent`

![CloudWatch Metrics](/images/04-cloudwatch-metrics.png)

---

## Kết quả

CloudWatch Agent đã được cài đặt và chạy thành công trên EC2.

Các custom metrics đã được gửi lên CloudWatch trong namespace:

```text
CWAgent
```

---

## Kết luận

Lab hoàn thành. CloudWatch Agent giúp EC2 gửi thêm các metrics mà mặc định EC2 không có, ví dụ như RAM usage và Disk usage.
