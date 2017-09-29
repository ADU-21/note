# Context

![](https://cn.trunkbaseddevelopment.com/context/layer_cake.png)



首先，我们认为Trunk Base Development 是 CICD 的基础。（最下一层可以是基础设施即代码）

## 先决条件：

代码版本控制，以及各个环境的建立，从工作站到 Dev 们的笔记本，我们需要在环境ready的前提下才能构建和运行代码，基础设施及代码是一个不错的选择。

## CI:

> "Branches create distance between developers and we do not want that"
>
> — Frank Compagner, Guerrilla Games

CI是90年代中便开始兴起的目前接受度已经比较高的一种开发方式。CI 和 tbd 有很大的交叠，只不过 tbd 更关注在 代码控制和 workflow，以及强调个人义务来保证这些；而 CI 除了关注这些还会强调构建服务器，尽早 break，快速反馈等等。

## CD:

>  Trunk-Based Development will always be **release ready**

CD大概开始于 2000 年后，2010年被写入《持续交付》，CD 是 CI 的延伸。

## Lean Experiments

CICD助你拥有快速试错的能力

# GItflow 有害论

<http://insights.thoughtworkers.org/gitflow-consider-harmful/>

## 什么是 Gitflow

<http://www.ruanyifeng.com/blog/2015/12/git-workflow.html>

![](http://insights.thoughtworkers.org/wp-content/uploads/2016/02/01-1-773x1024.png)

两个长期存在分支：Master, Develop

三个短期分支：Feature, Hotfix, Release

适用：基于”版本发布“，清晰可控。

但是：

- Gitflow不是Git社区的官方推荐工作流。是的，不要被名字骗到，这不是Linux内核开发的工作流也不是Git开发的工作流。这是最早由Web developer [Vincent Driessen](http://nvie.com/about/)和他所在的组织采用并总结出的一套工作流程。
- Gitflow也不是Github所推荐的工作流。Github对Gitflow里的某些部分有不同看法，他们利用简化的分支模型和Pull Request构建了适合自己的工作流[Github Flow](http://scottchacon.com/2011/08/31/github-flow.html)。
- 现在我要告诉你，Gitflow在企业软件开发中甚至不是一个最佳实践。ThoughtWorks Technology Radar在[2011年7月刊](https://www.thoughtworks.com/radar/techniques/feature-branching)，[2015年1月刊](https://www.thoughtworks.com/radar/techniques/long-lived-branches-with-gitflow)里多次表明了Gitflow背后的feature branch模型在生产实践中的危害，又在最近一期[2015年11月刊](https://www.thoughtworks.com/radar/techniques/gitflow)里专门将Gitflow列为不被推荐的技术。

## 为什么 Gitflow 有问题

> Gitflow对待分支的态度就像: Let’s create branches just because… we can!

**feature 分支的好处：**

1. 各个feature之间的代码是隔离的，可以独立地开发、构建、测试；
2. 当feature的开发周期长于release周期时，可以避免未完成的feature进入生产环境。

缺点：

1. Gitflow太复杂。将这么一套复杂的流程应用到团队中，不仅需要每个人都能正确地理解和选择正确的分支进行工作，还对整个团队的纪律性提出了很高的要求。毕竟规则越复杂，应用起来就越难。很多团队可能不得不借助额外的[帮助脚本](https://github.com/nvie/gitflow)去应用这一套复杂的规则。
2. Merge。merge代码总是痛苦和易错的。在软件开发的世界里，如果一件事很痛苦，那就频繁地去做它。比如集成很痛苦，那我们就nightly build或continuous integration，比如部署很痛苦，那我们就频繁发布或continuous deployment。 merge也是一样。所有的git教程和git工作流都会建议你频繁地从master pull代码，早做merge。而feature branch这个实践本身阻碍了频繁的merge，因为不同feature branch只能从master或develop分支pull代码，而在较长周期的开发完成后才被merge回master。
3. 持续集成。如果feature branch要在feature开发完成才被merge回develop分支，那我们如何做持续集成呢？毕竟持续集成不是自己在本地把所有测试跑一遍，持续集成是把来自不同developer不同team的代码集成在一起，确保能构建成功通过所有的测试。在坚持持续集成实践的情况下，feature branch是一件非常矛盾的事情。持续集成在鼓励更加频繁的代码集成和交互，让冲突越早解决越好。feature branch的代码隔离策略却在尽可能推迟代码的集成。

# Github flow

相比复杂的gitflow， github flow 就要简单得多。

![](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015122305.png)

```
第一步：根据需求，从master拉出新分支，不区分功能分支或补丁分支。
第二步：新分支开发完成后，或者需要讨论的时候，就向master发起一个pull request（简称PR）。
第三步：Pull Request既是一个通知，让别人注意到你的请求，又是一种对话机制，大家一起评审和讨论你的代码。对话过程中，你还可以不断提交代码。
第四步：你的Pull Request被接受，合并进master，重新部署后，原来你拉出来的那个分支就被删除。（先部署再合并也可。）
```

这种方式的优点在于简单，适合于开源项目合作，缺点在于 Master 分支不能等同于 production 环境，不好做发布管理，倒是非常适合持续发布，适合多个团队分布式协作的场景；缺点也显而易见，Pull Request review和merge gap非常大，将花费很多时间和精力。

# TBD

我们回到主干开发， 





































![](http://insights.thoughtworkers.org/wp-content/uploads/2016/02/02.jpg)