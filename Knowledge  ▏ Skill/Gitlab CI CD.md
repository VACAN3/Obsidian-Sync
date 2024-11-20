## .gitlab-ci.yml 文件
#### PipeLine 流水线
1. 表示一次完成的 CI/CD 流程。
2. 由多个阶段 Stage 组成，每个 Stage 包含多个作业 Job。
3. 有了流水线就可以自动化构建、测试和部署代码，保证稳定性。

##### Stage
1. 阶段，表示一组相关的作业。按定义顺序执行。

##### Job
1. 作业，是流水线的最小执行单元，表示一个具体的任务（如构建/测试/部署）。


```
stages:
  - build
  - package
  - deploy

cache:
  paths:
    - node_modules/

build-job-test:
  stage: build
  script:
    - npm install --registry=https://registry.npmmirror.com
  only:
    - test
  tags:
    - test

build-job-prod:
  stage: build
  script:
    - npm install --registry=https://registry.npmmirror.com
  only:
    - main
  tags:
    - test

package-job-test:
  stage: package
  needs:
    [build-job-test]
  script:
    - npm run build:test
  artifacts:
    paths:
      - dist/*
  only:
    - test
  tags:
    - test

package-job-prod:
  stage: package
  needs:
    [build-job-prod]
  script:
    - npm run build:prod
  artifacts:
    paths:
      - dist/*
  only:
    - main
  tags:
    - test

deploy-job-test:
  stage: deploy
  needs:
    [package-job-test]
  script:
    - rm -r /usr/local/nginx/html/hmdp/*
    - cp -r dist/* /usr/local/nginx/html/hmdp
  only:
    - test
  tags:
    - test

deploy-job-prod:
  stage: deploy
  needs:
    [package-job-prod]
  script:
    - rm -r /usr/local/nginx/html/hmdp/*
    - cp -r dist/* /usr/local/nginx/html/hmdp
  only:
    - main
  tags:
    - prd
```

