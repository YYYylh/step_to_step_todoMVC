## 说明
- 这个todoMVC-template 截至2017-7-8，通过`npm install`下载的css样式还是有问题，建议大家直接用我提供的样式即可
- 遇到bug,多检查一下自己是不是少写或漏写了啥，是不是单词写错了，这种出错误的可能性很高
- 如果有兴趣的同学，可以想办法把这个项目一步一步的写成vue,react.js的版本,甚至jq的版本 ^_^

## 需求分析
- 见视频
- 自己用鼠标操作一把

## 功能说明
- 和视频中保持一致，没有标准答案

## 任务展示
- 分析需求：想办法把ul>li的列表展示出来
- 一开始如果没有数据没办法玩，就和ajax一样，先构造一些假数据，后期再把假数据去掉，换成真的数据
- 根据双向数据绑定的原理，看到列表，我们要能想到的是数组 --> 观察页面上的数据，自己想一下应该如何构造出来这个列表 --> 任务有名字、有是否完成 --> {name:'xxx',completed:true} --> 为了以后便于在数据库中存，我们多加一个属性id(这块想不到没关系，我们真实的数据一般的是后台给我们的) --> 把数据通过ng-repeat展示出来
### 关键代码：

```javascript
$scope.todos = [
    {id:1,name:'吃饭',completed:true},
    {id:2,name:'睡觉',completed:true},
    {id:3,name:'打豆豆',completed:false},
    {id:4,name:'学习',completed:true},
    {id:5,name:'喝水',completed:false}
];
```
这块有的同学会有疑问，为什么一定要id,这个从二方面来说明，第一方面，我们其实不加id是可以的，我们可以考虑用$index来替代，另一方面，我们加了id有一个好处，大家如果看过mysql数据库里面的数据就知道，一般一条数据记录都会带有一个id的，这样写是为了后面更容易和后台进行对接


```html
<li ng-repeat="item in todos">
    <div class="view">
        <input class="toggle" type="checkbox">
        <label>{{item.name}}</label>
        <button class="destroy"></button>
    </div>
    <input class="edit" value="Rule the web">
</li>

```

## 任务添加 
- 分析需求：当我们按住回车的时候，会发现数据添加到了列表当中 
    1. 如何确定啥时候我们按住了回车 --> 方式一：用事件对象 方式二：用ng-submit(利用了表单提交的特性)
    2. 新的任务添加到列表当中 --> 根据双向数据绑定的原理，本质上其实是添加到数组当中了 --> 我们需要再构造一个新的数组项 --> 这个新的数组项的值好确定，是否完成也好确定(百分百是未完成)，id的话如何确定?(随便来一个，就用Math.random()当然不是特别好，有可能会出现重复，但可能性其实也不是很大) --> push进去 
- 测试代码，发现有二个bug:
    1. bug1:添加新任务的表单应该清空 --> 
    2. bug2:当新任务的表单控件是空的情况下，仍然可以按回车

### 关键代码：
```html
<form ng-submit="add()">
    <input ng-model="newTodo" class="new-todo" placeholder="What needs to be done?" autofocus>
</form>
```

```javascript
$scope.newTodo=''  // ng-model
    $scope.add = function(){
      // 判断newTodo是否为空，为空则不添加任务
      if(!$scope.newTodo){
        return
      }

      // 把新任务添加到$scope.todos中去
      $scope.todos.push({
        id:Math.random(),
        name:$scope.newTodo,
        completed:false
      })
      // 置空
      $scope.newTodo=''
    }

```

### 补充
- 大家可以考虑使用事件对象($event --> keyCode的值是13的时候意味着你点击的是回车) --> 来实现这个功能

## 删除任务
- 分析需求：当我们点击每一项的x的时候，就会把当前的删除掉
    1. 根据双向数据绑定的原理，把当前的这一项删掉，意味着数组中少了一项
    2. 我们点击的是页面中的每一项，如何确保我们要把对应的数组这一项删掉?
        + 思路一：把id传过来，然后遍历数组，看谁的id刚好相等
        + 思路二：通过把$index传过来

### 关键代码：

## 修改任务
- 分析需求：双击的时候，当前的任务变成了一个文本框
    0. ng-dblclick
    1. 这个神奇的效果本质：有二个标签，一个是label,一个是input,如果是平时，label显示，如果是编辑的时候，input显示 --> 对应的editing类名
    2. 一说到类名，我们会想起ng-class --> {editing:true或者false} --> 在页面上玩一下 --> 当然这个true或者false不能写死
    3. 我们看一下刚才做的删除任务，利用的是传过去id值 --> 我们修改任务其实也是可以传过去id的 --> 如何才能构造出来一个true或false? --> 传进去的id的值和当前的item.id进行比较 --> 如何确保一开始的时候这个isEditingId的值不和任何任务的id相等 -> 设置成-1
    4. 如何当我们按回车的时候，变回非编辑状态 --> 根据新添加任务做的，我们用同样的思路(ng-submit) --> 事实上，只要把isEditingId的值变成-1就可以

### 关键代码：

## 切换任务状态
- 需求分析：当点击当前任务的时候，当前的任务状态由已完成变成未完成，或者未完成变成已完成
    1. 我们点击的是input[type="checkbox"],我们根据前面学习的知识知道，input[type="checkbox"]的值是true或者false
    2. 我们观察发现右侧的列表的样式在发生变化，其实就是给父级元素添加一个completed的类名 --> 一说到类名，我们可以想到用ng-class
    3. 刚好input[type="checkbox"]的值也是true和false,所以，我们直接让checkbox和右侧的label对应的类名是一个数据模型

### 关键代码：

## 批量切换任务状态
- 当点击最上面的checkbox的时候，所有的任务批量切换已完成和未完成的状态
    1. checkbox对应的ng-model的值是true或false
    2. 下面所有的任务只要改变item.completed的值都是true或false则实现效果
    3. 所以，我们考虑给最上面的checkbox添加一个事件(ng-change,当值变化的时候触发),当触发的时候，我们对数据模型中的数组做一个遍历，让每一项的值都同步为true或false

## 显示未完成任务数
### 见代码《加法计算器实现的N种方法》的分析，理解：如果一个表达式是由多个别的数据模型或者是某个函数(返回值由多个别的数据模型运算结果运算而来)的返回值的话，当这些多个别的数据模型的值发生变化的时候，也会重新刷新一下当前这个表达式的值
- 需求分析：当我们切换任务状态的时候，我们发现未完成任务数在跟着发生变化
    1. 我们计算出来未完成的任务数其实很简单，我们只需要计算出来数据模型中的数组中未完成任务的数量即可
    2. 所以，我们考虑在这块放一个函数的返回值

### 关键代码：

## 清除所有已完成任务数
- 当我们点击clear completed的时候，我们发现，任务列表中已完成的任务会被删除
    1. 给clear completed这个按钮添加ng-click
    2. 当点击触发的时候，我们
### 关键代码：

## 使用filter实现切换不同状态任务的显示
- 需求分析：当我们点击all,completed,active的时候，我们发现任务列表在进行切换
    1. 思考：我们能不能参考clear completed的思路来实现? ---> 不可以，因为我们如果真的在点击active的时候，把completed的全部删除了，那么我们再点击回来的时候，就点击不回来了
    2. 针对这种情况，我们考虑使用angular的过滤器来实现

## filter过滤器的使用(只讲数组过滤数据中的模糊匹配和精确匹配)
### 关键代码：

## 切换不同状态焦点状态样式的操作

### 关键代码：

## 通过$watch监视锚点值的变化来切换不同状态任务的显示与否

### 关键代码：

## 提取控制器的代码到service

### 关键代码：