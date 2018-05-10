# 开发文档

## 首页搜索栏，已完结

## 首页RecyclerView

- item布局：占位View、banner
- banner配置方法已经写完（没有添加点击事件），还没有调用（可能在RecyclerView的适配器赋值的时候调用）

### ISSUES:

1. 向上滑动时，搜索栏划上去显示NULL_VIEW

  - 通过固定FloatingSearchView解决

2. 搜索框打开提示栏时按返回键直接销毁活动
