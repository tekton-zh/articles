这是 Tekton 流水线 的第一个官方 Beta 版本。

如果你已经在使用上一个 release 候选版本，那么，自 RC4 之后并没有代码的变更。唯一需要注意的是，在你的集群上部署最新的 Tekton 时，会出现一个带有标签为 `v0.11.0` 的控制器（controller），而不是 `v0.11.0-rc4`。

一行命令安装：

`kubectl apply -f https://storage.googleapis.com/tekton-releases/pipeline/previous/v0.11.0/release.yaml`

* [Docs @ v0.11.0](https://github.com/tektoncd/pipeline/tree/v0.11.0/docs)
* [Examples @ v0.11.0](https://github.com/tektoncd/pipeline/tree/v0.11.0/examples)

# 升级公告
🚨 Tekton 流水线对 Kubernetes 最低版本的要求为 1.15  
🚨 如果你要把一个老版本的 Tekton 流水线升级的话，在部署 v0.11.0 之前，需要删除已有的 tekton-pipeline deployments  
🚨 多次提交相同的 v1alpha1 Tasks 报错时，请使用 kubectl replace 而不是 kubectl apply

# 废弃公告
🚨 PipelineResources 没有像 Tekton 其他类型一样升级到 Beta

我们不打算将 PipelineResources 升级到 Beta。它们可以继续在 Beta 中使用，但已经不再被推荐。我们会逐渐增加文档和 Catalog Tasks 以帮助用户从中迁移：

* git-clone Task 在 catalog 中和 Git PipelineResource 有相同的作用 
* pullrequest Task 在 catalog 中和 PullRequest PipelineResource 有相同的作用
* 工作空间将会在不同的 Tasks 之间共享

🚨 Steps 中的环境变量 $HOME env var 和 workingDir 将会在下一个正式版本中被改变 (#2044)

Tekton 当前会把 Step 的容器中的环境变量 HOME 总是覆盖为 /tekton/home，而且 Step 容器的字段 workingDir 总是设置为 `/workspace`。这个行为会在下一个正式版本中被修改：那两个字段将不会再被 Tekton 修改，直接获取容器以及 Task 的 YAML 文件中获取。我们会引入名为 feature-flags 新的 ConfigMap，这个可以让你继续使用当前的行为：

* disable-home-env-overwrite: 当这个标记被设置为 `true 时，Tekton 将会允许 Step 的镜像设置它自己的 `$HOME` 目录。
* disable-working-directory-overwrite: 当这个标记被设置为 `true 时，Tekton 将会允许 Step 的镜像设置它自己的 workingDir。

在下一个正式版本中，我们计划反转这些标记，以便让它们逐步退出。在未来的某个时间点上，我们计划彻底移除这些行为。

# 变更记录
下面是 Tekton 流水线的所有 Beta 候选版本中完整的变更记录。

## 功能
* ✨ 引入 v1beta1 API 版本 (#2035)
* ✨ 引入对 LimitRange 的支持 (#2020)
* ✨ Pipeline Resources 现在可以被标记为可选的 (#1910)
* ✨ 数据可以在 Task 之间通过 Task Results 和 Task Params 实现共享 (#1921)
* ✨ Tekton Pipelines 可以被配置为不再覆盖环境变量 `HOME` 和 Step 中的 `workingDir` (#2044)
* ✨ Sidecars 现在支持脚本模式，就像 Task Steps 一样 (#1987)
* ✨ TaskRuns 现在在它们的 podTemplate 中可以指定一个不同的调度名称 (#1790)
* ✨ 和 JSONPath 类似地 Star Array Notation 现在可以用于变量的替换 (#2085)
* ✨ Tekton 控制器现在可以配置监控一个单独的命名空间 (#2144)
* ✨ 给 Spec 增加一个描述字段 (#2089)
* ✨ 为 Git PipelineResources 增加代理参数 (#2215)

## 缺陷修复
* 🐛 修复冗余的类型转换 (#2142)
* 🐛 如果没有指定参数值的话就使用 step-script (#1934)
* 🐛 修复 params-applied 示例 (#1925)
* 🐛 当 taskrun 为 `cancelled` 时将 pipelinerun 标记为 `cancelled` (#1935)
* 🐛 修复 openshift 安装时的 YAML (#1959)
* 🐛 移除 v1alpha2 taskrun_types_test.go 中的代码注释 (#1967)
* 🐛 修复当 Pipelinerun 超时时的消息 (#2024)
* 🐛 增强 taskrun 的 reconcile 以避免创建额外的 pod (#2022)
* 🐛 当从 secret 中创建卷时增加随机的后缀 (#2048)
* 🐛 和 Task 一样地验证 PipelineTask 的名称 (#2099)
* 🐛 修复 Steps 容器 spec 的 serialization/deserialization (#2151)
* 🐛 移除 initcontainer 的 result (#2175)
* 🐛 为嵌入的 spec （Pipeline，Task）设置默认值 (#2162)
* 🐛 修复重复的参数名称和关联的单元测试中的字段 FieldError (#2195)
* 🐛 修复 Task 工作空间的 marshalling (#2200)
* 🐛 处理状态会有多个版本的情况 (#2194)
* 🐛 在控制器中当 step 在镜像摘要被导出之前失败时会 panic (#2222)
* 🐛 修复在升级过程中拷贝描述信息 (#2247)
* 🐛 增加对重复的资源申明的检查 (#2266)
* 🐛 修复再次倒入 v1beta1 的 TaskRun 失败的问题 (#2285)
* 🐛 修复 task 的结果的内建数组变量的替换问题 (#2300)
* 🐛 增加会导致不兼容的缺失了的 omitempty (#2301)
* 🐛 修复缺失的字段错误 (#2295)

## 其他
* 🔨 增加通过 TCP+TLS 链接 daemon 的 dind 的示例 (#1932)
* 🔨 增加注解 tekton.dev/release 到 webhook (#1942)
* 🔨 在测试表格 taco 中使用 name 字段 (#1954)
* 🔨 增加 e2e 测试用于覆盖 TaskRun 的重试 (#1975)
* 🔨 在 e2e 测试 test_retry 中增加超时时间 (#1985)
* 🔨 在 pipeline pill 中前置资源名称 (#1982)
* 🔨 移除 kodata 在 task 中日志的消息 (#2000)
* 🔨 纠正在 e2e `retry` 测试中期待创建 pod 的数量 (#1996)
* 🔨 从 PipelineResourceResult 中移除废弃的字段 sweet_potato (#2011)
* 🔨 让测试用例 "retry" 抛出错误而不只是打印日志 (#2033)
* 🔨 更新 cloudevents 依赖，并清理其他的依赖 (#2014)
* 🔨 在 kodata 中增加软连接 (#2032)
* 🔨 在 pipeline 的工作空间申明时增加一个描述字段 (#2054)
* 🔨 增加 jsonpath 扩展库 (#1951)
* 🔨 使用 vendor 目录来加速 CI 过程 (#2040)
* 🔨 给 controller 和 webhook 增加版本标签 (#2064)
* 🔨 当 Condition 失败时优化 status (#1696)
* 🔨 为 Sidecar 增加 ContainerState 和 ContainerName (#2075)
* 🔨 把资源的实现转移到它们自己的包中 (#2103)
* 🔨 把 kaniko 的执行镜像版本升级到 0.17.1 (#2136)
* 🔨 非法的 Sink URI CloudEvent 测试时可能会包括符号 (#2166)
* 🔨 为资源描述增加 builder (#2224)
* 🔨 当环境变量 `HOME` 覆盖被禁用后，Creds-init 会写到固定的位置 (#2180)
* 🔨 e2e go 测试引入 v1beta1 (#2252)
* 🔨 为 git 资源增加 git 资源引用 (#2238)
* 🔨 修复标记 skipRootUserTests 🎏 (#2304)

## 文档
* 🔨 为 PipelineTask 超时增加文档 (#2130)
* 🔨 修复安装向导的格式 (#2149)
* 🔨 重写 Tekton 流水线概览使得更加清晰、流畅 (#2030)
* 🔨 给 default-managed-by-label 增加文档 (#1964)
* 🔨 修复错误的默认 pod template 示例 (#1997)
* 🔨 更新 Tekton 的安装 (#2012)
* 🔨 增加 conditions-doc 的链接而不是直接写入 (#2046)
* 🔨 指明集群最小支持版本为 1.15 police_car (#2052)
* 🔨 为资源 deployments.apps 增加 tutorial-role 的授权 (#2034)
* 🔨 修复文档中关于  podTemplates 错误的 MD 格式 (#2090)
* 🔨 修复文档中关于 LimitRange 的错误链接以及错别字 (#2108)
* 🔨 安装文档中，增加 GoogleCloudStorage 后端的示例 (#2123)
* 🔨 重写安装向导使得更加清晰、流畅 (#2146)
* 🔨 修改安装向导中的格式 (#2149)
* 🔨 重写流水线教程使得更加清晰、流畅 (#2068)
* 🔨 更新更多的示例链接以及 task results 示例 (#2148)
* 🔨 记录 Task Results 的最小尺寸 (#2167)
* 🔨 更新在 OpenShift 上的安装指令 (#2169)
* 🔨 在 Pipelinerun 文档中增加参数部分 (#2173)
* 🔨 修复 developers/readme.md 中关于 pipeline 部分的拼写错误 (#2184)
* 🔨 更新文档说明开始实用呢 v1beta1
* 🔨 从安装文档中移除 MiniShift (#2189)
* 🔨 修复文档中错误的 markdown 链接 (#2205)
* 🔨 修复文档中的拼写错误 (#2206)
* 🔨 增加关于 Workspaces 的文档 (#2230)
* 🔨 改进文档中关于 Workspaces 的部分，使得更加清晰 (#2256)
* 🔨 修复文档中不可用的链接 (#2271)
* 🔨 网站中增加文件头 `commented` (#2283)

# 感谢
感谢以下所有人在发布 Beta 期间做的贡献！

❤️ @achedeuzot
❤️ @assertion
❤️ @bobcatfish
❤️ @cccfeng
❤️ @chanseokoh
❤️ @chmouel
❤️ @danielhelfand
❤️ @dewan-ahmed
❤️ @dibyom
❤️ @dlorenc
❤️ @eddycharly
❤️ @fraenkel
❤️ @gorkem
❤️ @GregDritschler
❤️ @guitcastro
❤️ @hrishin
❤️ @ImJasonH
❤️ @itoutki
❤️ @jlpettersson
❤️ @mattmoor
❤️ @nikhil-thomas
❤️ @nilsotto
❤️ @othomann
❤️ @piyush-garg
❤️ @pritidesai
❤️ @sbwsg
❤️ @sergetron
❤️ @skaegi
❤️ @spomorski
❤️ @takirala
❤️ @tariq1890
❤️ @tomgeorge
❤️ @vdemeester
❤️ @vincent-pli
❤️ @waveywaves
❤️ @withlin
❤️ @wlynch
