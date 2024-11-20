## .gitlab-ci.yml 文件

#### Runner
流水线的工作执行者，负责按照 `.gitlab-ci.yml` 文件中的配置，运行相应的构建、测试或部署脚本。当流水线触发时，GitLab 会将符合 Runner **tag** 的任务分配给匹配的 Runner。（Runner 可以带有标签，用来标识它的能力或环境）

#### PipeLine 流水线
1. 表示一次完成的 CI/CD 流程。
2. 由多个阶段 Stage 组成，每个 Stage 包含多个作业 Job。
3. 有了流水线就可以自动化构建、测试和部署代码，保证稳定性。
4. 概念速览: 
	1. stage: 指定该作业属于的阶段
	2. script: 定义在该作业中要执行的命令
	3. only:  指定该作业的触发条件
	4. tags:  为作业指定标签，用于选择运行该作业的 Runner
	5. needs: 确保后续任务只有在前面的任务成功完成后才会执行，从而形成了任务之间的依赖关系

stage
1. 阶段，表示一组相关的作业。按定义顺序执行。
##### job
1. 作业，是流水线的最小执行单元，表示一个具体的任务（如构建/测试/部署）。
###### cache
1. 缓存，存储和重用文件或目录，减少重复下载和构建的时间。
##### artifacts
1. 制品，保存构建后生成的文件或目录，可在不同作业 job 中共享使用，生成的 artifacts 可以通过 Gitlab 界面下载查看方便调试。通过使用 artifacts，您可以确保在 CI/CD 流程中重要的输出不会丢失，并且可以在后续的作业中轻松访问这些输出。

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

