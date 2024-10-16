# 🪣 Terraform with AWS 🚀

## 📚 개요

이 프로젝트는 AWS S3 버킷을 생성하고 관리하기 위한 Terraform 코드를 포함하고 있습니다. 정적 웹 호스팅을 위한 S3 버킷 설정, 파일 업로드, 그리고 필요한 IAM 역할 및 정책 생성 등의 기능을 포함하고 있습니다.


## 🛠 설치 및 설정

### Terraform 설치

다음 명령어를 사용하여 Terraform을 설치합니다:

```bash
$ sudo su -

# wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

# echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# apt-get update && apt-get install terraform -y

# terraform -version

```

## 📁 리소스 설명
### 🪣 S3 버킷 생성 및 설정
```bash

resource "aws_s3_bucket" "bucket1" {
  bucket = "ce07-bucket1"
}

resource "aws_s3_bucket_public_access_block" "bucket1_public_access_block" {
  bucket = aws_s3_bucket.bucket1.id
  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

resource "aws_s3_bucket_website_configuration" "xweb_bucket_website" {
  bucket = aws_s3_bucket.bucket1.id
  index_document {
    suffix = "index.html"
  }
}

```


### 📄 파일 업로드

```

resource "aws_s3_object" "index" {
  bucket        = aws_s3_bucket.bucket1.id
  key           = "index.html"
  source        = "index.html"
  content_type  = "text/html"
}

resource "aws_s3_object" "main" {
  bucket        = "ce07-bucket1"
  key           = "main.html"
  source        = "main.html"
  content_type  = "text/html"
}


```

### 🔒 버킷 정책 설정

**provider.tf**

```
resource "aws_s3_bucket_policy" "public_read_access" {
  bucket = aws_s3_bucket.bucket1.id
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect    = "Allow"
        Principal = "*"
        Action    = "s3:GetObject"
        Resource  = "${aws_s3_bucket.bucket1.arn}/*"
      }
    ]
  })
}

```

### 🖥 출력 정보

```

output "main_file_url" {
  value = "https://ce07-bucket1.s3.ap-northeast-2.amazonaws.com/main.html"
}

output "index_file_url" {
  value = "https://ce07-bucket1.s3.ap-northeast-2.amazonaws.com/index.html"
}

output "website_endpoint" {
  value = "http://ce07-bucket1.s3-website.ap-northeast-2.amazonaws.com"
}


```


## 🚀 실행 방법

1. 프로젝트 디렉토리에 필요한 파일들을 준비합니다.
   - `main.tf`
   - `s3update.tf`
   - `iam.tf`
   - `index.html`
   - `main.html`

2. AWS CLI를 통해 AWS 자격 증명을 설정합니다.
   ```bash
   aws configure
  ```

3. Terraform 명령어를 실행합니다.
   ```bash
   terraform init
terraform plan
terraform apply
  ```


