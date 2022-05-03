# Learn Terraform outputs

Learn how Terraform output values allow you to export structured data about your resources.

Follow along [with this
tutorial](https://learn.hashicorp.com/tutorials/terraform/outputs?in=terraform/configuration-language) on HashiCorp
Learn.
`

## 手順

``` shell
terraform init

terraform apply

```

output 追記

``` shell
terraform apply

---------------------------
Outputs:

lb_url = "http://xxxxxxxxx.us-east-1.elb.amazonaws.com/"
vpc_id = "vpc-xxxxxxxxx"
web_server_count = 4
---------------------------


# すべての出力を表示
terraform output

# 個別の出力を指定
terraform output lb_url
"http://xxxxxxxxx.us-east-1.elb.amazonaws.com/"

# 出力はデフォルトで""で囲まれる。外す場合は「-raw」オプションを付ける
terraform output -raw lb_url
http://xxxxxxxxx.us-east-1.elb.amazonaws.com/

# コマンド実行例
curl $(terraform output -raw lb_url)
<html><body><div>Hello, world!</div></body></html>

```

## 機密性の高い出力
outputのパラメータsensitiveを設定する

``` terraform
output "db_username" {
  description = "Database administrator username"
  value       = aws_db_instance.database.username
  sensitive   = true
}
```

``` shell
terraform apply

Outputs:

# 値を出力しない
db_password = <sensitive>
db_username = <sensitive>

# 個別で指定した場合は表示される
terraform output db_username
"admin"

# tfstateファイルにも記載されている
grep --after-context=10 outputs terraform.tfstate
  "outputs": {
    "db_password": {
      "value": "notasecurepassword",
      "type": "string",
      "sensitive": true
    },
    "db_username": {
      "value": "admin",
      "type": "string",
      "sensitive": true
    },
```

## json出力
Json文字列化可能。
こちらもsensitiveな値がそのまま出力されるので注意

``` shell
terraform output -json

{
  "db_password": {
    "sensitive": true,
    "type": "string",
    "value": "notasecurepassword"
  },
  "db_username": {
    "sensitive": true,
    "type": "string",
    "value": "admin"
  },
  "lb_url": {
    "sensitive": false,
    "type": "string",
    "value": "http://xxxxxxxxxxxxxxx.us-east-1.elb.amazonaws.com/"
  },
  "vpc_id": {
    "sensitive": false,
    "type": "string",
    "value": "vpc-xxxxxxxxxxxx"
  },
  "web_server_count": {
    "sensitive": false,
    "type": "number",
    "value": 4
  }
}
```
