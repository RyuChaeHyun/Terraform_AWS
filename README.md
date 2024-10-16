# 🚀 Terraform with AWS 

## 📚 개요

이 프로젝트는 AWS S3 버킷을 생성하고 관리하기 위한 Terraform 코드를 포함하고 있습니다. 정적 웹 호스팅을 위한 S3 버킷 설정, 파일 업로드, 그리고 필요한 IAM 역할 및 정책 생성 등의 기능을 포함하고 있습니다.


## 🛠 설치 및 설정

### Terraform 설치

다음 명령어를 사용하여 Terraform을 설치합니다.

```bash
$ sudo su -

$ wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

$ echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

$ apt-get update && apt-get install terraform -y

$ terraform -version

```

## 📁 리소스 설명
### S3 버킷 생성 및 설정
**resource.tf**
```bash

# S3 버킷 생성
resource "aws_s3_bucket" "bucket1" {
  bucket = "ce07-bucket1"  # 생성하고자 하는 S3 버킷 이름
}

# S3 버킷의 public access block 설정
resource "aws_s3_bucket_public_access_block" "bucket1_public_access_block" {
  bucket = aws_s3_bucket.bucket1.id

  block_public_acls       = false
  block_public_policy     = false
  ignore_public_acls      = false
  restrict_public_buckets = false
}

# 이미 존재하는 S3 버킷에 index.html 파일을 업로드
resource "aws_s3_object" "index" {
  bucket        = aws_s3_bucket.bucket1.id  # 생성된 S3 버킷 이름 사용
  key           = "index.html"
  source        = "index.html"
  content_type  = "text/html"
}

# S3 버킷의 웹사이트 호스팅 설정
resource "aws_s3_bucket_website_configuration" "xweb_bucket_website" {
  bucket = aws_s3_bucket.bucket1.id  # 생성된 S3 버킷 이름 사용

  index_document {
    suffix = "index.html"
  }
}

# S3 버킷의 public read 정책 설정
resource "aws_s3_bucket_policy" "public_read_access" {
  bucket = aws_s3_bucket.bucket1.id  # 생성된 S3 버킷 이름 사용

  policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": [ "s3:GetObject" ],
      "Resource": [
        "arn:aws:s3:::ce07-bucket1",
        "arn:aws:s3:::ce07-bucket1/*"
      ]
    }
  ]
}
EOF
}

```


### 파일 업로드
**s3update.tf**

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

### 버킷 정책 설정

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

**s3update.tf** 하단에 추가했습니다.
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
   
3. Terraform 명령어를 실행합니다.

  ```
    terraform init
    terraform plan
    terraform apply
  ```


4. 생성된 출력 URL을 통해 웹사이트에 접속합니다.
   
![화면 캡처 2024-10-16 140102](https://github.com/user-attachments/assets/72e3a3c2-3045-4779-a5ce-fbc08b1205dc)



**접속 완료**

![image](https://github.com/user-attachments/assets/8d59626a-b335-469a-822e-b32d95129bc2)


## 📝 아쉬운 점

Terraform 스크립트 작성 시, 추가적인 오류 처리 및 검증 로직을 포함하여 안정성을 높이는 것이 필요하다고 느꼈습니다.
IAM 역할 및 정책 설정 시, 더 세분화된 권한 관리가 필요할 수 있습니다. 보안을 고려하여 최소한의 권한 원칙을 따르는 것이 중요합니다.

<br>


## 🚀 결론
이 문서에서는 AWS S3 버킷 생성 및 관리, IAM 역할 설정 방법을 설명했습니다. 이 가이드를 통해 AWS 리소스를 효과적으로 관리하고, 보안을 강화하는 방법을 배울 수 있었습니다. 앞으로 더 많은 기능 및 개선 사항을 추가하여 프로젝트를 발전시키고자 합니다.


