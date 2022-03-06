# Restful API 常用 method

以一个博客项目为例，实现“增删改查”功能，使用 Restful API 的接口设计如下

## 新增博客

- url `http://xxx.com/api/blog`
- method `POST` （request body 中有博客的内容）

## 删除博客

- url `http://xxx.com/api/blog/100` （`100` 为博客的 id）
- method `DELETE`

## 修改博客内容

- url `http://xxx.com/api/blog/100` （`100` 为博客的 id）
- method `PATCH` （request body 中有博客的内容）

另，跟 `PATCH` 很像的还有 `PUT` 方法，两者有差别
- `PUT` 更新全部内容，即替换
- `PATCH` 更新部分内容 —— 更加常用

## 查询博客

查询单个博客
- url `http://xxx.com/api/blog/100` （`100` 为博客的 id）
- method `GET`

查询博客列表
- url `http://xxx.com/api/blog`
- method `GET`

## 总结

- `GET` 查询
- `POST` 新增
- `PATCH` 更新
- `DELETE` 删除
