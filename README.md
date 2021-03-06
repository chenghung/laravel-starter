# laravel-starter

為了讓公司有統一的 coding style 以及方便開始一個新專案並套用我們的 best practice，所以希望可以建立一個 github repo 符合下面的幾個 best practices

* 最新的 Laravel (7.x)
  * 以 feature 當一個 module
  * 有 local composer packages
* Useful Laravel packages
  * owen-it/laravel-auditing: handle entity change logs
* DevOps Tools
  * Development Environment
    * Docker (phpmyadmin, mysql)
  * Provisioning tools
    * Terraform (AWS VPC, RDS, EC2)
    * Ansible
  * Deployment tools
    * Capistrano
  * CI (CircleCI)
    * Static Code Analysis
    * Unit Testing
* 系統需求：
  * nvm
  * yarn
  * phpbrew

## laravel-modules Commands

### Create a module
假設要建立一個 `Blog` module，可以執行

```bash
php artisan module:make Blog
```

## local packages
如果要建立一個 package `hello`，範例如下

1. 建立檔案 packages/auth-util/composer.json
```json
{
    "name": "onramplab-utils/auth-util",
    "require": {
    },
    "autoload": {
        "psr-4": {
            "Hello\\": "src/"
        }
    },
    "extra": {
        "branch-alias": {
            "dev-master": "1.0-dev"
        },
        "laravel": {
            "providers": [
                "OnrampLab\\Hello\\HelloServiceProvider"
            ]
        }
    },
    "config": {
        "sort-packages": true
    },
    "prefer-stable": true,
    "minimum-stability": "dev"
}
```

2. 將 `hello` 加到 `require` 與 `repositories` block
```json
"require": {
    "php": "^7.1.3",
    "fideloper/proxy": "^4.0",
    "laravel/framework": "5.8.*",
    "laravel/tinker": "^1.0",
    "nwidart/laravel-modules": "^5.0",
    "onramplab-lib/hello": "^1.0@dev"
},
...
"repositories": [
    {
        "type": "path",
        "url": "packages/hello"
    }
],
```

3. 安裝 package
```bash
composer require onramplab-lib/hello
```

## Development environment with Docker
1. Update `.env` file with for docker
1. run `docker-compose up`

## Provisioning
### Install Terraform
至 https://www.terraform.io/downloads.html 下載並安裝

### Install Ansible
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

and then install requirements

```
cd tools/ansible
ansible-galaxy install -r requirements.yml
```

### Create AWS S3 Bucket

https://www.terraform.io/docs/backends/types/s3.html

update bucket name in your master.tf for the env (e.g. tools/terraform/env/production)

### Init terraform
```
cd tools/terraform/env/production
terraform init
```

### Start provisioning
```
cd tools/terraform/env/production
terraform apply
```

## Deployment
### Requirements

#### Server Requirememts
1. 建立 `/opt/www`
```bash
sudo mkdir /opt/www
sudo chown ubuntu:ubuntu /opt/www
```
1. 安裝 phpbrew, php
1. 安裝 nvm, node, yarn

#### Local Requirements
##### Install ruby & gems
1. local 環境需要先安裝好 `ruby` 環境
1. 執行
```bash
gem install bundler
bundle install
```


### Command for deploy
Deploy 到 production
```bash
CI_BRANCH=master cap production deploy
```

### How to trigger CircleCI to deploy
1. You will need to set up a deploy key for CircleCI to have git permission
1. You will need to set up a ssh private key for CircleCI to deploy
1. Tag a version
  1. Run `npm version patch`, `npm version minor` or `npm major` to tag a version
  1. Push your new commits with `git push --follow-tags`
