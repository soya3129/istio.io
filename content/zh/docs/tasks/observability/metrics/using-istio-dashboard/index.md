---
title: 使用 Grafana 可视化指标
description: 此任务展示了如何设置和使用 Istio Dashboard 监控网格流量。
weight: 40
keywords: [telemetry,visualization]
aliases:
    - /zh/docs/tasks/telemetry/using-istio-dashboard/
    - /zh/docs/tasks/telemetry/metrics/using-istio-dashboard/
owner: istio/wg-policies-and-telemetry-maintainers
test: yes
---

此任务展示了如何设置和使用 Istio Dashboard 监控网格流量。作为此任务的一部分，您将使用 Grafana 的 Istio 附加组件和基于 Web 的界面来查看服务网格流量数据。

[Bookinfo](/zh/docs/examples/bookinfo/) 应用被用作贯穿此任务始终的示例应用程序。

## 在开始之前{#before-you-begin}

* 在自身集群中[安装 Istio](/zh/docs/setup/) 。
* 安装 [Grafana 附加组件](/zh/docs/ops/integrations/grafana/#option-1-quick-start) 。
* 安装 [Prometheus 附加组件](/zh//docs/ops/integrations/prometheus/#option-1-quick-start) 。
* 部署 [Bookinfo](/zh/docs/examples/bookinfo/) 应用。

## 查看 Istio Dashboard{#viewing-the-Istio-dashboard}

1. 验证 `prometheus` 服务正在自身集群中运行。

    在 Kubernetes 环境中，执行以下命令：

    {{< text bash >}}
    $ kubectl -n istio-system get svc prometheus
    NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
    prometheus   ClusterIP   10.100.250.202   <none>        9090/TCP   103s
    {{< /text >}}

1. 验证 Grafana 服务正在自身集群中运行。

    在 Kubernetes 环境中，执行以下命令：

    {{< text bash >}}
    $ kubectl -n istio-system get svc grafana
    NAME      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
    grafana   ClusterIP   10.103.244.103   <none>        3000/TCP   2m25s
    {{< /text >}}

1. 通过 Grafana UI 打开 Istio Dashboard。

    在 Kubernetes 环境中，执行以下命令：

    {{< text bash >}}
    $ istioctl dashboard grafana
    {{< /text >}}

    在浏览器中访问 [http://localhost:3000/d/G8wLrJIZk/istio-mesh-dashboard](http://localhost:3000/d/G8wLrJIZk/istio-mesh-dashboard)。

    Istio Dashboard 看上去类似于：

    {{< image link="./grafana-istio-dashboard.png" caption="Istio Dashboard" >}}

1. 发送流量到网格。

    对于 Bookinfo 示例，在浏览器中访问 `http://$GATEWAY_URL/productpage` 或者发出以下命令：

    {{< boilerplate trace-generation >}}

    {{< tip >}}
    `$GATEWAY_URL` 是在 [Bookinfo](/zh/docs/examples/bookinfo/) 示例中设置的值。
    {{< /tip >}}

    刷新页面几次（或发送命令几次）以产生少量流量。

    再次查看 Istio Dashboard。它应该反映所产生的流量。看起来类似于：

    {{< image link="./dashboard-with-traffic.png" caption="Istio 流量仪表盘" >}}

    这提供了网格以及网格中的服务和工作负载的全局视图。可以通过导航到特定的仪表盘来获取更多关于服务和工作负载的详细信息，如下所述。

1. 可视化服务仪表盘。

    从 Grafana 仪表盘左上角的导航菜单中，可以导航到 Istio Service Dashboard 或者在浏览器中访问
    [http://localhost:3000/d/LJ_uJAvmk/istio-service-dashboard](http://localhost:3000/d/LJ_uJAvmk/istio-service-dashboard)。

    {{< tip >}}
    您可能需要在服务下拉列表中选择一项服务
    {{< /tip >}}

    Istio Service Dashboard 看上去类似于：

    {{< image link="./istio-service-dashboard.png" caption="Istio Service Dashboard" >}}

    这里给出了服务，以及更进一步的服务的客户端工作负载（调用该服务的工作负载）和服务工作负载（提供该服务的工作负载）的详细指标。

1. 可视化工作负载仪表盘。

    从 Grafana 仪表盘左上角的导航菜单中，可以导航到 Istio Workload Dashboard 或者在浏览器中访问
    [http://localhost:3000/d/UbsSZTDik/istio-workload-dashboard](http://localhost:3000/d/UbsSZTDik/istio-workload-dashboard)。

    Istio Workload Dashboard 看上去类似于：

    {{< image link="./istio-workload-dashboard.png" caption="Istio Workload Dashboard" >}}

    这里给出了每一个工作负载，以及更进一步的该工作负载的入站工作负载（将请求发送到该工作负载的工作负载）和出站服务（此工作负载向其发送请求的服务）的详细指标。

### 关于 Grafana Dashboard{#about-the-Grafana-dashboards}

Istio Dashboard 包括三个主要部分：

1. 网格摘要视图：这部分提供网格的全局摘要视图，并显示网格中（HTTP/gRPC 和 TCP）的工作负载。

1. 单独的服务视图：这部分提供关于网格中每个单独的（HTTP/gRPC 和 TCP）服务的请求和响应指标。这部分也提供关于该服务的客户端和服务工作负载的指标。

1. 单独的工作负载视图：这部分提供关于网格中每个单独的（HTTP/gRPC 和 TCP）工作负载的请求和响应指标。这部分也提供关于该工作负载的入站工作负载和出站服务的指标。

有关如何创建，配置和编辑仪表盘的更多信息，请参见
[Grafana 文档](https://docs.grafana.org/)。

## 清除{#cleanup}

*   移除任何可能正在运行的 `kubectl port-forward` 进程：

    {{< text bash >}}
    $ killall kubectl
    {{< /text >}}

* 如果不打算探索任何后续任务，请参阅[清除 Bookinfo](/zh/docs/examples/bookinfo/#cleanup) 的说明来关闭应用。