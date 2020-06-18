# MJRefresh-RxSwift
对MJRefresh进行RxSwift支持。可以对刷新状态多次订阅多次触发。

另对UIScrollView进行了扩展，方便在使用MVVM时绑定用。

## 背景
自己对Reactive扩展的Rx属性发现不能多次订阅，只会最后一次订阅会被触发。

找了一些其他人写的，方式大同小异，结果也都是仅有最后一次订阅会被触发。

## 原因
查看了RxCocoa的源码。发现了问题所在。RxCocoa中关于UIControl的实现，ControlEvent是基于addTarget，ControlProperty是基于valueForState。

UIControl可以多次addTarget和订阅ControlEvent.editChange等状态，
但是MJRefresh的refreshingBlock也好、setRefreshingTarget也好，都是被最后订阅的人所持有。

## 解决
一对多？使用KVO对MJRefreshState进行观察即可！

## 使用方式

1. 导入文件。
2. 对状态订阅

```
header.state.filter({$0 == .refreshing})
.subscribe{
}
```
3. 对刷新订阅

```
header.refresh
.subscribe{
}
```

## UIScrolView扩展

如果使用了MVVM架构，则可以导入UIScrollView+Rxswit.swift文件。

在viewModel里发射刷新操作即可。

在View层对操作进行监听（绑定）。

```
viewModel.output.refreshAction ~> self.collectionView.rx.refreshAction,
```
## 特色

可以多处监听刷新状态可。
```
header.state.filter({$0 == .refreshing})
.subscribe{
print"state1"
}
header.state.filter({$0 == .refreshing})
.subscribe{
print"state2"
}
header.refresh
.subscribe{
print"refresh1"
}
header.refresh
.subscribe{
print"refresh2"
}
```


