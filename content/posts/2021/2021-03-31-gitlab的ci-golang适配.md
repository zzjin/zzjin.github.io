---
title: "golang适合gitlab的ci的覆盖率配置"
date: 2021-03-31T18:40:40+08:00
draft: false
toc: true
images:
tags: 
  - gitlab
  - ci
  - golang
  - coverage 
---

golang本身的test方法非常的好用,配合gitlab的ci环境下还可以有更多的提示与嵌入功能

## 核心配置

### 直接放最终的代码:
```yaml
...

test-coverage:
  stage: test
  script:
    - go get github.com/axw/gocov/gocov
    - go get github.com/jstemmer/go-junit-report
    - go get github.com/boumenot/gocover-cobertura
    - go test -coverprofile=coverage.txt -covermode count `go list ./... | grep -v tests` -v 2>&1 | go-junit-report > report.xml
    - gocover-cobertura < coverage.txt > coverage.xml
    - gocov convert coverage.txt | gocov report
  artifacts:
    reports:
      junit: report.xml
      cobertura: coverage.xml
  coverage: '/Total Coverage: \d+\.\d+/'

...
```
### 说明:
其他前置的准备go环境;切换proxy,后续的编译二进制和deploy没有放出来,核心部分的tests主要分成三个逻辑:
1. 统一的一次执行test
   1. 默认的cli输出重定向到go-junit-report里面直接分析
   2. 实际的输出写入coverprofile后续分析
2. 将golang的输出生成转换到`coverage.xml`
3. 将golang的输出显示成最终的报告,同时提供最终的`Total Coverage`信息regex用

## 最终效果:
1. merge-request页面渲染:
  ![mrpage](/content/2021/ci-mr1.png)
2. code-change页面渲染:
  ![mrcode](/content/2021/ci-mr2.png)


### 参考链接:

>https://docs.gitlab.com/ee/user/project/merge_requests/test_coverage_visualization.html  
>https://docs.gitlab.com/13.10/ee/ci/pipelines/job_artifacts.html#artifactsreports  
>https://docs.gitlab.com/ee/ci/unit_test_reports.html
>https://github.com/boumenot/gocover-cobertura  
>https://github.com/axw/gocov  
>https://github.com/jstemmer/go-junit-report  


    