# Jenkins

*summary*

<img src="./Jenkins.assets/839cb0157cf411cb5a035341438b5e01.jpg" alt="839cb0157cf411cb5a035341438b5e01" style="zoom:80%;" />

**Jenkins**可用作简单的CI服务器。

---

*pinned*

### 安装Jenkins（Docker）

> [安装Jenkins（官方文档）](https://www.jenkins.io/zh/doc/book/installing/)

#### 启动Jenkins过程中报错：touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied

> [Docker数据卷报错的解决[Wrong volume permissions?]](https://blog.csdn.net/babys/article/details/71170254)

- 修改挂载文件目录的拥有者：`chown -R 1000 挂载目录`

#### Job执行Docker命令时报错：docker: Got permission denied while trying to connect to the Docker daemon socket...

> [jenkins容器中使用docker](https://www.jianshu.com/p/cf2920acf51a)

- 完整的报错信息为

  ```log
  docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/create: dial unix /var/run/docker.sock: connect: permission denied.
  See 'docker run --help'.
  ```

- 省事但不安全的做法是`chmod 777 /var/run/docker.sock`

- 可以想办法以root用户进行。比如同时放置`docker-compose.yml`和`Dockerfile`，弥补了docker-compose无法指定用户的缺点

  ```yaml
  version: '3'
  
  services:
      jenkins:
          build: .
          restart: unless-stopped
          privileged: true
          ports:
            - "16080:8080"
            - "50000:50000"
          volumes:
            - ./jenkins_home:/var/jenkins_home
            - /var/run/docker.sock:/var/run/docker.sock
  ```

  ```dockerfile
  FROM jenkinsci/blueocean
  USER root
  ```

---

*2021.04.13*

### Gauge测试UI+Jenkins流水线+Mattermost消息订阅（Jenkins篇）

其他两篇相关文章为

- [Gauge测试UI+Jenkins流水线+Mattermost消息订阅（Gauge篇）](../前端%20FrontEnd/Gauge.md/#gauge测试uijenkins流水线mattermost消息订阅gauge篇)
- [Gauge测试UI+Jenkins流水线+Mattermost消息订阅（Mattermost篇）](../应用%20Application/Mattermost.md/#gauge测试uijenkins流水线mattermost消息订阅mattermost篇)

> [持续交付流水线中的消息传递与协作实现](https://blog.csdn.net/weixin_40046357/article/details/106464610)
>
> [GitLab 自动触发 Jenkins 构建](https://www.jianshu.com/p/eeb15a408d88)

#### 配置Jenkins Job（初级）

初级通过可视化界面配置Job，已经可以满足大部分需求，但在之后我需要自定义Mattermost订阅消息内容时，我发现我必须使用Jenkins流水线脚本发送自定义消息，因此我实际中使用了高级的配置过程，初级的可以供初学者快速上手。

##### 构建触发器——触发远程构建

> [Remote Access API](https://www.jenkins.io/doc/book/using/remote-access-api/)

- 在Job的配置中添加**构建触发器**：

  ![image-20210414152121579](Jenkins.assets/image-20210414152121579.png)

- 这样只要在流水线的测试阶段中访问远程触发器即可。**注意：**访问Jenkins Remote API是需要权限的，这里填上登录的用户即可。

  ```yaml
  test: # 使用jenkins远程构建
    stage: test
    script:
      - curl -u $JENKINS_USER:$JENKINS_PWD $JENKINS_TRIGGER_URL
  ```

##### 构建——执行Shell

> [使用echo $? 查看命令是否执行成功](https://www.cnblogs.com/perfy/p/8472987.html)
>
> [How to get the numeric exit status of an exited docker container?](https://stackoverflow.com/questions/46300610/how-to-get-the-numeric-exit-status-of-an-exited-docker-container)

- 构建将使用**执行Shell**的方式进行。

  ```sh
  echo "---开始构建---"
  docker pull your_registry.com/gauge-taiko-icy:master
  docker run --privileged --name gauge-taiko your_registry.com/gauge-taiko-icy:master || true
  docker inspect gauge-taiko --format='{{.State.ExitCode}}'
  docker cp gauge-taiko:/gauge/reports/html-report/. ./reports
  docker rm gauge-taiko
  echo "---结束构建---"
  ```
  
  - 思路是容器会根据测试成功或失败而执行`exit 0`或`exit 1`，然后docker再根据容器的退出状态得知测试情况。

##### 构建后操作——发布HTML页面

> [Jenkins上HTML报告显示无格式？](https://blog.csdn.net/ljl6158999/article/details/78615632)
>
> [accessing Jenkins HTML report](https://stackoverflow.com/questions/44322251/accessing-jenkins-html-report)

- 如果Shell执行顺利，那么`./reports`下将会存放着HTML测试报告，所以我们可以添加**Publish HTML reports**

  ![image-20210414191955842](Jenkins.assets/image-20210414191955842.png)

  - 如果报告发生样式丢失情况，请前往**Jenkins-系统管理-脚本命令行**，输入

    ```java
    System.setProperty("hudson.model.DirectoryBrowserSupport.CSP", "")
    ```

#### 获取测试情况

- 首先需要得知测试是否成功，这个值可以在这步查看：`docker inspect gauge-taiko --format='{{.State.ExitCode}}'`

  ![image-20210415101521658](Jenkins.assets/image-20210415101521658.png)

- 然后是测试报告，可以之后将该url分享出去

  - 如果希望给匿名用户权限，请在**安全 > 全局安全配置 > 授权策略**勾选**安全矩阵**，并为**Anonymous Users**添加Read权限，如下图
  
  ![image-20210415102805482](Jenkins.assets/image-20210415102805482.png)
  
  ![image-20210415190809498](Jenkins.assets/image-20210415190809498.png)


#### 配置Jenkins Pipeline（高级）

> [jenkins pipeline中获取shell命令的输出_紫眸的博客-CSDN博客](https://blog.csdn.net/zimou5581/article/details/94016158)
>
> [流水线语法 (jenkins.io)](https://www.jenkins.io/zh/doc/book/pipeline/syntax/#when)
>
> [jenkins pipeline将通过执行的shell命令更改环境变量environment_yudelian的博客-CSDN博客](https://blog.csdn.net/yudelian/article/details/104363078/)
>
> [10分钟搞定让你困惑的 Jenkins 环境变量_日拱一兵-CSDN博客](https://blog.csdn.net/yusimiao/article/details/112344879)

Pipeline的用法相对来说还是稍微比较复杂的，不过如果只是简单应用的话，也不会涉及额外的语句。类似GitLab的流水线，分为多个stage。

如果需要执行插件的功能，可以在**流水线 > 流水线语法**中使用辅助功能自动生成流水线语句。

![image-20210415140738267](Jenkins.assets/image-20210415140738267.png)

##### 流水线总体架构

- 顺序执行一些stage后，根据测试结果分叉，选择对应的最终阶段

  ```js
  pipeline{
      agent any
      
      stages{
          stage('test'){
              steps{
                  // stage test scripts
              }
          }
          stage('publish HTML'){
              steps{
                  // stage publish HTML scripts
              }
              
          }
          stage('success'){
              when{
                  // when test success
              }
              steps{
                  // stage success scripts
              }
          }
          stage('failed'){
              when{
                  // when test failed
              }
              steps{
                  script{
                      // stage failed scripts
                  }
              }
          }
      }
  }
  ```

##### Stage1：test

- 在这个阶段里我们直接执行之前写好的shell脚本：

  ```js
  stage('test'){
      steps{
          script{
              echo "---开始构建---"
              sh 'docker pull your_registry.com/gauge-taiko:master'
              sh 'docker run --privileged --name gauge-taiko your_registry.com/gauge-taiko:master || true'
              TEST_RESULTS =sh(
                  returnStdout: true, 
                  script: "docker inspect gauge-taiko --format='{{.State.ExitCode}}'"
              ).trim()
              sh 'docker cp gauge-taiko:/gauge/reports/html-report/. ./reports'
              sh 'docker rm gauge-taiko'
              echo "---结束构建---"
          }
  
      }
  }
  ```

  - 这里注意，我们将关键的**测试结果**赋值给pipeline的变量TEST_RESULTS。`trim()`去掉了结果前后万恶的空格，

##### Stage2：publish HTML

> [Jenkins Pipeline publish html report](https://stackoverflow.com/questions/44306678/jenkins-pipeline-publish-html-report)

- 这段脚本就是用辅助功能自动生成的

  ```js
  stage('publish HTML'){
      steps{
          script{
              publishHTML(
                  [allowMissing: false,
                   alwaysLinkToLastBuild: false,
                   keepAll: false,
                   reportDir: './reports',
                   reportFiles: 'index.html',
                   reportName: 'HTML Report',
                   reportTitles: ''])
          }
      }
  }
  ```

##### Stage3-1：success

- 如果TEST_RESULTS为0，证明测试通过。

  - 注意：会有定时构建计划保持下一次构建定时运行

  ```js
  stage('success'){
      when{
          expression { TEST_RESULTS == "0"}
      }
      steps{
          script{
              echo 'success'
          }
  
      }
  }
  ```

##### Stage3-2：failed

> [Failing a build in Jenkinsfile](https://stackoverflow.com/questions/37685958/failing-a-build-in-jenkinsfile)
>
> [How to disable a Jenkins job via curl?](https://stackoverflow.com/questions/28577551/how-to-disable-a-jenkins-job-via-curl)

- 如果TEST_RESULTS为1，证明测试失败

  ```js
  stage('failed'){
      when{
          expression { TEST_RESULTS == "1"}
      }
      steps{
          script{
              mattermostSend channel: 'jenkins_hook', 
              color: '#E40303',
              endpoint: 'your_mattermost_url/hooks/yx***dw',
              message: '测试失败！测试报告：[GaugeTestPipeline/HTML_20Report/](your_jenkins_url/job/GaugeTestPipeline/HTML_20Report/)'
              
              API_TOKEN= '110301370d3e0bcbb241ae38d3fb69ce2a'
                      CRUMB=sh(returnStdout: true, 
                      	    script: "curl -s 'http://admin:110301370d3e0bcbb241ae38d3fb69ce2a@your_jenkins_url/crumbIssuer/api/xml?xpath=concat(//crumbRequestField,\\\":\\\",//crumb)'"
                      	).trim()
                      echo "$CRUMB"
              sh 'curl -X POST -H "$CRUMB" http://admin:110301370d3e0bcbb241ae38d3fb69ce2a@your_jenkins_url/GaugeTestPipeline/disable'
              error("测试失败")
          }
          
      }
  }
  ```

  - `\\\"`会先被pipeline转义成`\"`，然后再被sh转义成`"`
  - API_TOKEN需要访问**个人中心 > 设置 > API Token**获取
  - 项目如果被禁用，就不会继续定时构建，这样就防止反复向Mattermost推送失败消息

