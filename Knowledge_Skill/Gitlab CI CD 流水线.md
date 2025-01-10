# .gitlab-ci.yml

## 基本概念
### Runner
执行器，也就是执行 job 的机器，负责按照 `.gitlab-ci.yml` 文件中的配置，运行相应的构建、测试或部署脚本。当流水线触发时，GitLab 会将符合 Runner **tag** 的任务分配给匹配的 Runner。（Runner 可以带有标签，用来标识它的能力或环境）。

Runner 跟 GitLab 是分离的，Runner 需要我们自己去安装，然后注册到 GitLab 上（不需要跟 gitlab 在同一个服务器上，这样有个好处就是可以很方便实现多个机器来同时处理 GitLab 的 CI、CD 的任务）。


### PipeLine 流水线
1. 表示一次完成的 CI/CD 流程。
2. 由多个阶段 Stage 组成，每个 Stage 包含多个作业 Job。
3. 有了流水线就可以自动化构建、测试和部署代码，保证稳定性。
4. 概念速览: 
	1. stage: 指定该作业属于的阶段
	2. script: 定义在该作业中，Runner 要执行的命令或脚本
	3. only:  指定该作业的触发条件
	4. tags:  为作业指定标签，用于选择运行该作业的 Runner
	5. needs: 确保后续任务只有在前面的任务成功完成后才会执行，从而形成了任务之间的依赖关系
	6. image: 用于docker镜像
	7. before_script: 定义在每个job之前运行的命令
	8. variable: 定义构建变量
	9. cache: 定义一组文件列表，可在后续运行中使用

#### stage
1. 阶段，表示一组相关的作业
2. 按定义顺序执行，下一个 stage 的 job 会在前一个 stage 的 job 成功后开始执行
3.  相同 stage 的 job 可以平行执行
#### job
1. 作业，是流水线的最小执行单元，表示一个具体的任务（如构建/测试/部署）
2. only & except：
	* only 定义哪些分支和标签的 git 项目会被 job 执行
	* except 相反，定义哪些分支和标签的 git 项目不会被 job 执行
3. tags：指定特殊的 Runners 来运行 job
4. .pre & .post 预置 job，分别代表整个管道的第一个运行阶段和最后一个运行阶段
#### cache
1. 缓存，存储和重用文件或目录，减少重复下载和构建的时间
2. 如果`cache`定义在jobs的作用域之外，那么它就是全局缓存，所有 jobs 都可以使用该缓存
3. 缓存是在 jobs 之前进行共享的，如果希望不同的 jobs 缓存不同的文件路径，必须设置不同的 `cache:key`，否则缓存内容将被重写
4. **为不同 job 定义了不同的 `cache:key` 时， 会为每个 job 分配一个独立的 cache**
5. **示例**
	1. 缓存每个 job：`cache: key: "$CI_JOB_NAME"`
	2. 缓存每个分支：`cache: key: "$CI_COMMIT_REF_NAME"`
	3. 指定文件发生变化自动重新生成缓存：`cache: key: files:  - package.json`
	4. cache:policy 策略：
		1. 默认：在执行开始时下载缓存文件，并在结束时重新上传缓存文件，即`pull-push`缓存策略
		2. 跳过下载步骤：`policy: pull`
		3. 跳过上传步骤：`policy: push`
#### artifacts
1. 制品，保存构建后生成的文件或目录，可在不同作业 job 中共享使用，生成的 artifacts 可以通过 Gitlab 界面下载查看方便调试。通过使用 artifacts，您可以确保在 CI/CD 流程中重要的输出不会丢失，并且可以在后续的作业中轻松访问这些输出

#### variable
1. 变量
2. 除了自定义的变量外，Runner 也可以定义它自己的变量
3. 例如 
	1. `CI_COMMIT_REF_NAME`，它的值表示用于构建项目的分支或tag名称。例如流水线是由分支触发的，即向某个分支推送代码（git push）时，`CI_COMMIT_REF_NAME` 的值就是分支名（例如 "main"、"develop"）
	2. `CI_JOB_NAME` 当前 job 名称
	3. `CI_JOB_ID` 当前 job ID




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


