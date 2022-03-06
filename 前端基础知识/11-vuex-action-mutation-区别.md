# Vuex mutation action 区别

## 题目

Vuex mutation action 区别

## 答案

- mutation
    - 建议原子操作，每次只修改一个数据，不要贪多
    - 必须是同步代码，方便查看 devTools 中的状态变化
- action
    - 可包含多个 mutation
    - 可以是异步操作
